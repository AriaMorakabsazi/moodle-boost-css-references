# Complete Moodle Boost Theme Renovation & CSS Reference Map
*A Technical Blueprint, Selector Directory, File Location Guide, and Design Specification for Gemini Argon*

---

## 1. System Overview & The 3-Stage SCSS Pipeline

Moodle 5.x Boost theme uses a 3-stage SCSS compilation pipeline. To renovate the theme without hacking core files or breaking Moodle upgrades, performance, and SEO, custom styling must be injected into the proper stage:

```
[theme_boost | scsspre]
  (Raw initial SCSS)
         │
         ▼
[theme_boost | preset]
  (default.scss / Bootstrap 5 / Design System Tokens)
         │
         ▼
[Moodle Core Modules SCSS]
  (58 individual moodle component stylesheets)
         │
         ▼
[theme_boost | scss]
  (Raw SCSS - Custom overrides, glassmorphism, animations)
         │
         ▼
[Compiled & Minified CSS Output]
  (Cached in moodledata/localcache/theme)
```

### Stage 1: `theme_boost | scsspre` (Raw initial SCSS)
- **Where to configure**: Site Administration ➔ Appearance ➔ Themes ➔ Boost ➔ Advanced settings ➔ `Raw initial SCSS` (`theme_boost/scsspre`).
- **PHP Handler**: `theme_boost_get_pre_scss()` in `moodle/public/theme/boost/lib.php`.
- **Purpose**: Defines SCSS variables **before** Bootstrap and Moodle core SCSS files are loaded. Any variable defined here (e.g., `$primary`, `$body-bg`, `$border-radius`) overrides the default values set with `!default`.
- **Rule for Gemini Argon**: Write only SCSS variable declarations here (no CSS selectors or rules).

### Stage 2: `theme_boost | preset` (Theme Preset)
- **Where to configure**: Site Administration ➔ Appearance ➔ Themes ➔ Boost ➔ General settings ➔ `Theme preset` (`theme_boost/preset`).
- **Default file**: `moodle/public/theme/boost/scss/preset/default.scss`.
- **Custom preset upload**: Site Administration ➔ Appearance ➔ Themes ➔ Boost ➔ General settings ➔ `Preset files` (`theme_boost/presetfiles`).
- **Purpose**: Imports Moodle Design System (MDS) tokens, Font stacks, FontAwesome, Bootstrap 5, and Moodle core components.

### Stage 3: `theme_boost | scss` (Raw SCSS)
- **Where to configure**: Site Administration ➔ Appearance ➔ Themes ➔ Boost ➔ Advanced settings ➔ `Raw SCSS` (`theme_boost/scss`).
- **PHP Handler**: `theme_boost_get_extra_scss()` in `moodle/public/theme/boost/lib.php`.
- **Purpose**: Injected **after** all core styles have compiled. Used for high-specificity selector rules, CSS variables overrides (`--bs-*`, `--mds-*`), card shadows, glassmorphism (`backdrop-filter`), hover animations, border treatments, and layout enhancements.
- **Rule for Gemini Argon**: Write standard CSS / SCSS rules here. You can reference variables declared in `scsspre` or `default.scss`.

---

## 2. Master File Location Map: "Which CSS is Found Where"

This index maps every visual element in Moodle 5.x to its exact stylesheet and Mustache template in the repository.

### A. Theme Boost SCSS Directory (`moodle/public/theme/boost/scss/`)

| File Path (relative to `moodle/public/theme/boost/scss/`) | Core Responsibility | Key Selectors & Variables |
| :--- | :--- | :--- |
| `preset/default.scss` | Baseline preset; imports Bootstrap & core; sets default variables | `$primary`, `$body-bg`, `$card-bg`, `$border-radius`, `$theme-colors` |
| `preset/plain.scss` | Unstyled minimal preset fallback | Minimal variables |
| `design-system.scss` | Imports modern Moodle Design System (MDS) tokens and components | `@import "design-system-tokens"`, CSS custom variables |
| `design-system-tokens.scss` | Bridges legacy SCSS variables with new MDS tokens | Tokens from `lib/bundles/design-system/scss/tokens/_index.legacy` |
| `bootstrap.scss` | Imports Bootstrap 5 library from Moodle core | `@import "../../../../lib/bootstrap5/scss/bootstrap"` |
| `fontawesome.scss` | Imports FontAwesome icons | `@import "../../../../lib/fonts/fontawesome/fontawesome"` |
| `moodle.scss` | Main aggregator importing all 58 core SCSS files below | Central `@import` list |

### B. Moodle Core SCSS Components (`moodle/public/theme/boost/scss/moodle/`)

| File Path (relative to `moodle/public/theme/boost/scss/moodle/`) | Visual Component | Key Selectors / Rules |
| :--- | :--- | :--- |
| `navbar.scss` | Fixed top navigation bar | `.navbar.fixed-top`, `.navbar-brand`, `.logo`, `#usernavigation`, `.usermenu` |
| `primarynavigation.scss` | Main navigation tabs in top bar | `.primary-navigation`, `.moremenu`, `.nav-link`, `.nav-item` |
| `drawer.scss` | Left course index & right block drawers | `.drawer.drawer-left`, `.drawer.drawer-right`, `.drawerheader`, `.drawertoggle` |
| `courseindex.scss` | Course index tree inside left drawer | `.courseindex`, `.courseindex-section`, `.courseindex-item`, `.pageitem`, completion dots |
| `course.scss` | Course content format (Topics/Weeks), Section cards | `.course-content`, `.course-section`, `.section-item`, `.activity-item`, `.activity-grid` |
| `secondarynavigation.scss` | Course tabs bar (Course, Settings, Grades) | `.secondary-navigation`, `.navigation.secondarynav-tabbar`, `.mds-nav-pill` |
| `tertiarynavigation.scss` | Action filter bars inside course pages | `.tertiary-navigation`, `.navitem`, action selectors |
| `login.scss` | Split-screen modern login page | `body.pagelayout-login`, `.login-layout-left`, `.login-layout-right`, `.login-container` |
| `blocks.scss` | Dashboard & sidebar widget cards | `.block`, `.card`, `.block_myoverview`, `.block_timeline`, `.block_calendar_month` |
| `courseoverview.scss` | My Courses overview tables & views | `#course-overview-page`, `.inplaceeditable-select` |
| `icons.scss` | Icon sizes & activity category coloring | `.activityiconcontainer`, `$activity-icon-colors`, `.icon` |
| `variables.scss` | Default Moodle variables | `$activity-icon-colors`, `$drawer-width`, `$navbar-height`, Atto vars |
| `buttons.scss` | Buttons, button groups, icon buttons | `.btn`, `.btn-primary`, `.btn-secondary`, `.btn-icon` |
| `forms.scss` | Form fields, labels, inputs, validation | `.form-control`, `.form-select`, `.fitem`, `.col-form-label`, `.custom-switch` |
| `dropdown.scss` | Dropdown menus and popovers | `.dropdown-menu`, `.dropdown-item`, `.dropdown-toggle` |
| `modal.scss` | Dialogs and pop-up modals | `.modal-content`, `.modal-header`, `.modal-footer`, `.modal-backdrop` |
| `toasts.scss` | Floating notifications | `#toast-wrapper`, `.toast`, `.toast-header`, `.toast-body` |
| `action-menu.scss` | Kebab / 3-dot action menus | `.action-menu`, `.action-menu-trigger`, `.dropdown-menu-right` |
| `calendar.scss` | Full calendar & event chips | `.calendarwrapper`, `table.calendartable`, `.calendar-event` |
| `message.scss` | Messaging flyout drawer & chat bubbles | `.message-drawer`, `.message`, `.day`, `.contact` |
| `popover-region.scss` | Top bar notification & chat bell popovers | `.popover-region`, `.popover-region-toggle`, `.count-container` |
| `sticky-footer.scss` | Floating bottom bar for grading / editing | `.stickyfooter`, `[data-region="sticky-footer"]` |
| `user.scss` | User profiles, user cards, avatars | `.userprofile`, `.userpicture`, `.user-box` |
| `dark.scss` | Dark color mode overrides | `[data-bs-theme="dark"]`, `@include dark-mode` |
| `colour-mode.scss` | Color mode picker & state persistence | `.colourmode-menu`, light/dark preference |
| `moremenu.scss` | Collapsible navigation overflow ("More") | `.moremenu`, `.dropdown-more-menu` |
| `layout.scss` | Main page scaffold and column sizing | `#page-wrapper`, `#page`, `#page-content`, `#region-main-box`, `#region-main` |

### C. Moodle Core Tokens & Templates Outside Theme Directory

| File Path (relative to `moodle/public/`) | Component | Description |
| :--- | :--- | :--- |
| `lib/bundles/design-system/scss/tokens/_light.scss` | MDS Design System Tokens | SCSS variables for typography scale, spacing (`$mds-scale-*`), semantic surfaces |
| `lib/bundles/design-system/scss/tokens/_index_css_vars.scss` | MDS CSS Custom Properties | CSS variables: `--mds-bg-surface-default`, `--mds-border-subtle`, `--mds-text-muted` |
| `course/templates/coursecard.mustache` | Dashboard Course Card | HTML template rendering `.card.course-card` inside My Overview |
| `course/templates/coursecards.mustache` | Course Cards Container | HTML grid template rendering cards list |
| `blocks/myoverview/templates/main.mustache` | My Overview Block Layout | Renders tab filters, search bar, and course card deck |
| `course/format/templates/local/content/section/item.mustache` | Course Section Card | HTML structure for section boxes, headers, collapse toggles, and summaries |
| `course/format/templates/local/content/cm/item.mustache` | Course Activity Item | HTML structure for activity cards (`.activity-item`), icons, badges, completion |
| `theme/boost/templates/drawers.mustache` | Master Layout | Master layout rendering body, navbar, left drawer, right drawer, main page |
| `theme/boost/templates/navbar.mustache` | Top Navigation Bar | Navbar HTML, logo, user menu, search form, dark mode button |
| `theme/boost/templates/login.mustache` | Split Login Layout | Master layout for login screen |

---

## 3. Deep-Dive UI Zones: Selectors, Templates, SCSS & Gemini Argon Prompts

---

### Zone 1: Top Navigation Bar (`.navbar.fixed-top`)

#### 1. Visual Purpose & Context
The primary header spanning the top of every page. It hosts the site brand/logo, primary links (*Home*, *Dashboard*, *My Courses*), global course search, notification bell, messaging drawer toggle, light/dark mode switcher, edit mode switch, and the user profile dropdown.

#### 2. Why Default Boost Looks "Pale"
- Flat white `#ffffff` background with no depth or elevation.
- Hard, dull `#dee2e6` 1px border on the bottom.
- Navigation links lack active pill indicators or hover transitions.
- Edit mode switch is a standard gray form switch.

#### 3. Exact Source Locations
- **HTML Template**: `moodle/public/theme/boost/templates/navbar.mustache`
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/navbar.scss`
- **Primary Nav SCSS**: `moodle/public/theme/boost/scss/moodle/primarynavigation.scss`

#### 4. DOM Tree & Selector Hierarchy
```
nav.navbar.fixed-top.bg-body.navbar-expand
 └── div.container-fluid
      ├── button.navbar-toggler (mobile toggle)
      ├── a.navbar-brand
      │    └── img.logo
      ├── div.primary-navigation
      │    └── div.moremenu
      │         └── ul.nav.more-nav
      │              ├── li.nav-item > a.nav-link (active: .active)
      │              └── li.dropdown.dropdown-more-menu (overflow)
      └── div#usernavigation.navbar-nav.ms-auto
           ├── div.simplesearchform (search box)
           ├── div.colourmode-menu (dark mode switcher)
           ├── div.editmode-switch-form (Edit mode toggle)
           ├── div.popover-region.popover-region-notifications
           ├── div.popover-region.popover-region-messages
           └── div.usermenu-container
                └── div.dropdown.usermenu
                     ├── a.dropdown-toggle
                     │    ├── span.userbutton
                     │    │    └── span.avatars > span.userpicture
                     │    └── span.usertext
                     └── div.dropdown-menu.dropdown-menu-right
```

#### 5. SCSS Variables to Target (`scsspre`)
```scss
$navbar-height: 64px;
$primary-nav-padding-x: 1rem;
$primary-nav-padding-y: 0.5rem;
```

#### 6. Custom CSS/SCSS Overrides (`scss`)
```scss
// Glassmorphic translucent navbar
.navbar.fixed-top {
    height: 64px;
    background: rgba(255, 255, 255, 0.88) !important;
    backdrop-filter: blur(12px) saturate(180%);
    -webkit-backdrop-filter: blur(12px) saturate(180%);
    border-bottom: 1px solid rgba(226, 232, 240, 0.8) !important;
    box-shadow: 0 4px 20px -2px rgba(15, 23, 42, 0.05);

    .navbar-brand {
        font-weight: 700;
        letter-spacing: -0.02em;
        .logo { max-height: 42px; }
    }

    .primary-navigation .nav-link {
        font-weight: 500;
        color: #475569;
        border-radius: 0.5rem;
        padding: 0.5rem 1rem;
        transition: all 0.2s ease;

        &:hover {
            color: $primary;
            background-color: rgba($primary, 0.08);
        }

        &.active {
            color: $primary;
            background-color: rgba($primary, 0.12);
            font-weight: 600;
        }
    }

    .editmode-switch-form .custom-control-input:checked ~ .custom-control-label::before {
        background-color: $primary;
        border-color: $primary;
        box-shadow: 0 0 10px rgba($primary, 0.4);
    }

    .userpicture {
        border-radius: 50%;
        box-shadow: 0 0 0 2px rgba($primary, 0.2);
    }
}
```

#### 7. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Modern SaaS top navigation bar for a premium learning platform (Moodle Boost redesign). Ultra-clean glassmorphism header with translucent frosted white acrylic background (85% opacity, backdrop blur), subtle slate-200 bottom hairline border, elegant company logo on the left, pill-shaped navigation links with active state highlighted in soft indigo, sleek pill search bar with search icon, circular notification bell with pink unread badge, smooth modern toggle switch for 'Edit Mode', and rounded user profile avatar with subtle indigo halo ring on the far right. Crisp vector typography, Figma UI design, 8k resolution, photorealistic web interface."*

---

### Zone 2: Course Index Left Drawer (`#theme_boost-drawers-courseindex`)

#### 1. Visual Purpose & Context
The persistent left-hand drawer when viewing a course. Displays the full hierarchical table of contents: sections, activities, completion status dots, and current reading location indicators.

#### 2. Why Default Boost Looks "Pale"
- Flat, dull gray background (`#e9ecef`) that clashes with white content cards.
- Text links are plain gray with no visual hierarchy.
- Section dividers and active item highlights use harsh, dated left-borders.

#### 3. Exact Source Locations
- **HTML Template**: `moodle/public/theme/boost/templates/drawer.mustache`
- **Course Index Template**: `moodle/public/course/format/templates/local/courseindex/`
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/courseindex.scss`
- **Drawer SCSS**: `moodle/public/theme/boost/scss/moodle/drawer.scss`

#### 4. DOM Tree & Selector Hierarchy
```
div#theme_boost-drawers-courseindex.drawer.drawer-left
 ├── div.drawerheader
 │    ├── div.drawerheading > #courseindexdrawerheading
 │    └── div.draweractions > button.drawertoggle.btn-icon
 └── div.drawercontent
      └── nav.courseindex
           └── div.courseindex-section[data-for="section"]
                ├── div.courseindex-section-title
                │    ├── button.courseindex-chevron
                │    └── a.courseindex-link
                └── div.courseindex-sectioncontent[data-for="sectioncontent"]
                     └── div.courseindex-item[data-for="cm"]
                          ├── a.courseindex-link
                          └── div.completioninfo
```

#### 5. SCSS Variables to Target (`scsspre`)
```scss
$drawer-left-width: 300px;
$drawer-left-bg-color: #ffffff;
$courseindex-link-color: #475569;
$courseindex-link-hover-color: #0f172a;
$courseindex-item-active-bg: #e0e7ff;
$courseindex-item-page-bg: #4f46e5;
$courseindex-item-radius: 0.625rem;
```

#### 6. Custom CSS/SCSS Overrides (`scss`)
```scss
#theme_boost-drawers-courseindex.drawer.drawer-left {
    background-color: #ffffff;
    border-right: 1px solid #e2e8f0;
    box-shadow: 4px 0 24px rgba(15, 23, 42, 0.04);

    .courseindex-section {
        border-radius: 0.625rem;
        margin: 0.25rem 0.5rem;
        transition: background-color 0.15s ease;

        &.current {
            background-color: rgba($primary, 0.05);
            border-left: 3px solid $primary;
        }
    }

    .courseindex-item {
        border-radius: 0.5rem;
        padding: 0.45rem 0.75rem;
        transition: all 0.15s ease;

        &:hover {
            background-color: #f1f5f9;
        }

        &.pageitem {
            background-color: rgba($primary, 0.12);
            color: $primary;
            font-weight: 600;

            .courseindex-link {
                color: $primary;
                font-weight: 600;
            }
        }
    }
}
```

#### 7. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Left sidebar course curriculum index panel for a modern LMS web application. Pristine pure white background with subtle right border. Clean collapsible module accordion list with small chevron icons, checkmark circles indicating completed activities, glowing indicator on currently selected lesson, modern typography in dark slate, smooth subtle rounded pill hover states. Professional educational UI mockup, Dribbble style, high fidelity, 4k."*

---

### Zone 3: Blocks Right Drawer (`#theme_boost-drawers-blocks`)

#### 1. Visual Purpose & Context
The collapsible right-hand drawer that houses contextual blocks (Mini-Calendar, Upcoming Events, Timeline, Latest Badges, Logged-in Users, Private Files).

#### 2. Why Default Boost Looks "Pale"
- Blocks appear as standard Bootstrap cards (`.card`) stacked with no shadow or breathing room.
- Gray card headers look bureaucratic.

#### 3. Exact Source Locations
- **HTML Template**: `moodle/public/theme/boost/templates/blocks-drawer.mustache`
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/blocks.scss`
- **Drawer Mechanics**: `moodle/public/theme/boost/scss/moodle/drawer.scss`

#### 4. DOM Tree & Selector Hierarchy
```
div#theme_boost-drawers-blocks.drawer.drawer-right
 ├── div.drawerheader
 │    └── div.draweractions > button.drawertoggle.btn-icon
 └── div.drawercontent
      ├── div.d-print-none > div.addblockbutton
      └── section.block.card[data-block="..."]
           ├── div.card-body
           │    ├── h5.card-title.d-inline
           │    ├── div.block-controls.header
           │    └── div.content
```

#### 5. SCSS Variables to Target (`scsspre`)
```scss
$drawer-right-width: 320px;
$drawer-right-bg-color: #f8fafc;
```

#### 6. Custom CSS/SCSS Overrides (`scss`)
```scss
#theme_boost-drawers-blocks.drawer.drawer-right {
    background-color: #f8fafc;
    border-left: 1px solid #e2e8f0;

    .block.card {
        border: 1px solid #e2e8f0;
        border-radius: 0.875rem;
        box-shadow: 0 1px 3px rgba(15, 23, 42, 0.05);
        background: #ffffff;
        margin-bottom: 1.25rem;
        transition: transform 0.2s ease, box-shadow 0.2s ease;

        &:hover {
            box-shadow: 0 8px 16px -2px rgba(15, 23, 42, 0.06);
            transform: translateY(-1px);
        }

        .card-title {
            font-weight: 700;
            font-size: 0.95rem;
            color: #1e293b;
        }
    }
}
```

#### 7. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Right side auxiliary drawer panel on a luxury web app interface, displaying widget cards including an interactive mini-calendar with vibrant date dots, a recent activity timeline with circular avatar icons, and upcoming deadlines widget. Floating white cards with subtle soft drop shadows, slate border-lines, 14px rounded corners, neat spacing, minimalist dashboard aesthetics."*

---

### Zone 4: Page Full Header & Breadcrumbs (`header#page-header`)

#### 1. Visual Purpose & Context
The banner region above the main content on every page. Houses the course title, course image, category breadcrumbs, and page-level action buttons.

#### 2. Why Default Boost Looks "Pale"
- Breadcrumbs are standard Bootstrap unstyled text separated by plain slashes (`/`).
- Title typography lacks weight and tight letter-spacing.

#### 3. Exact Source Locations
- **HTML Render**: `output.full_header` in core Moodle renderer (`moodle/public/lib/outputrenderers.php`).
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/core.scss` (around line 1200+).

#### 4. DOM Tree & Selector Hierarchy
```
header#page-header.row
 └── div.col-12.pt-3.pb-3
      ├── div.card
      │    └── div.card-body
      │         ├── div.d-flex
      │         │    ├── nav[aria-label="Breadcrumb"]
      │         │    │    └── ol.breadcrumb
      │         │    │         ├── li.breadcrumb-item > a
      │         │    │         └── li.breadcrumb-item.active
      │         │    └── div.page-header-actions
      │         └── div.d-flex.align-items-center
      │              ├── div.page-header-image
      │              └── div.page-header-headings
      │                   └── h1.h2
```

#### 5. Custom CSS/SCSS Overrides (`scss`)
```scss
header#page-header {
    padding: 1.5rem 0 1rem 0;

    .breadcrumb {
        background: transparent;
        padding: 0;
        margin-bottom: 0.75rem;

        .breadcrumb-item {
            font-size: 0.85rem;
            color: #64748b;

            a {
                color: #64748b;
                text-decoration: none;
                transition: color 0.15s ease;
                &:hover { color: $primary; }
            }

            &.active {
                color: #0f172a;
                font-weight: 600;
            }

            + .breadcrumb-item::before {
                content: "›";
                color: #94a3b8;
                font-size: 1.15rem;
                line-height: 1;
                vertical-align: middle;
            }
        }
    }

    .page-header-headings h1 {
        font-size: 1.85rem;
        font-weight: 800;
        letter-spacing: -0.03em;
        color: #0f172a;
    }
}
```

#### 6. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Hero section header of an academic course overview page. Features sleek chevron breadcrumb trail ('Courses > Computer Science > CS101 Advanced Python'), bold modern sans-serif course headline title 'CS101: Advanced Software Engineering', enrolled student badge count pill, and an 'Action' button with an indigo gradient fill and subtle drop shadow. Clean, polished, tech-forward UI layout."*

---

### Zone 5: Secondary Navigation Tabs (`.secondary-navigation`)

#### 1. Visual Purpose & Context
The horizontal tab bar beneath the header that lets teachers and students switch between *Course*, *Settings*, *Participants*, *Grades*, *Reports*, and *More*.

#### 2. Why Default Boost Looks "Pale"
- In stock Boost, they use plain flat underline tabs or basic `.nav-tabs` which look like 2010-era intranet tabs.

#### 3. Exact Source Locations
- **HTML Template**: `moodle/public/theme/boost/templates/drawers.mustache` (line 141) & `moodle/public/lib/templates/secondarymoremenu.mustache`.
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/secondarynavigation.scss`.

#### 4. DOM Tree & Selector Hierarchy
```
div.secondary-navigation.d-print-none
 └── div.navigation.secondarynav-tabbar
      └── ul.nav.more-nav
           ├── li.nav-item > a.nav-link.mds-nav-pill (active: .active / .mds-nav-pill--selected)
           └── li.dropdown.dropdown-more-menu
```

#### 5. Custom CSS/SCSS Overrides (`scss`)
```scss
.secondary-navigation {
    margin-bottom: 1.5rem;

    .navigation.secondarynav-tabbar {
        border-bottom: 1px solid #e2e8f0;
        background: transparent;

        .more-nav {
            gap: 0.5rem;
        }

        .nav-link,
        .mds-nav-pill {
            border: none;
            border-radius: 0.5rem;
            color: #64748b;
            font-weight: 500;
            padding: 0.55rem 1rem;
            transition: all 0.2s ease;

            &:hover {
                color: #0f172a;
                background-color: #f1f5f9;
            }

            &.active,
            &.mds-nav-pill--selected {
                color: $primary;
                background-color: rgba($primary, 0.1);
                font-weight: 600;
                position: relative;

                &::after {
                    content: '';
                    position: absolute;
                    bottom: -1px;
                    left: 15%;
                    width: 70%;
                    height: 2.5px;
                    background-color: $primary;
                    border-radius: 2px;
                }
            }
        }
    }
}
```

#### 6. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Segmented pill navigation bar for course management tabs: 'Course', 'Settings', 'Participants (42)', 'Grades', 'Reports'. Active tab styled in vibrant indigo fill with soft glow, inactive tabs in muted slate gray on hover, smooth pill shape, high resolution web application header design."*

---

### Zone 6: Course Section Cards & Topic Layout (`.course-section`)

#### 1. Visual Purpose & Context
The central container for course modules. Formatted into Topics or Weeks, each containing section titles, descriptions, and grouped learning activities.

#### 2. Why Default Boost Looks "Pale"
- Stock Moodle wraps sections in thin gray outlines with flat white interiors and zero box-shadow.
- Highlighted current section uses an ugly 3px left border bar (`.current::before`).

#### 3. Exact Source Locations
- **HTML Template**: `moodle/public/course/format/templates/local/content/section/item.mustache`
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/course.scss` (lines 100-260).

#### 4. DOM Tree & Selector Hierarchy
```
div.course-content
 └── ul.section-list
      └── li.course-section[data-for="section"] (current: .current)
           └── div.section-item
                ├── div.course-section-header
                │    ├── button.section-collapsemenu (Expand/Collapse)
                │    ├── h3.sectionname > a
                │    ├── div.sectionbadges > span.badge
                │    └── div.section_action_menu
                └── div.course-content-item-content
                     ├── div[data-for="sectioninfo"] > div.summarytext
                     ├── div.availabilityinfo
                     └── div.section-content-inner
                          └── ul.section[data-for="cmlist"]
                               └── li.activity
```

#### 5. Custom CSS/SCSS Overrides (`scss`)
```scss
.course-section {
    margin-top: 1.5rem;

    .section-item {
        background-color: #ffffff;
        border: 1px solid #e2e8f0;
        border-radius: 1rem;
        box-shadow: 0 4px 6px -1px rgba(15, 23, 42, 0.04), 0 2px 4px -2px rgba(15, 23, 42, 0.02);
        overflow: hidden;
        transition: box-shadow 0.2s ease;

        &:hover {
            box-shadow: 0 10px 15px -3px rgba(15, 23, 42, 0.06);
        }

        .course-section-header {
            background-color: #ffffff;
            padding: 1.25rem 1.5rem;

            .sectionname a {
                font-weight: 700;
                font-size: 1.25rem;
                color: #0f172a;
            }
        }

        .section-content-inner {
            background-color: #f8fafc;
            border-top: 1px solid #f1f5f9;
            padding: 1.25rem;
        }
    }

    &.current > .section-item {
        border-color: rgba($primary, 0.5);
        box-shadow: 0 0 0 1px rgba($primary, 0.2), 0 8px 20px -4px rgba($primary, 0.1);

        &::before {
            display: none; // Removes legacy harsh left bar
        }
    }
}
```

#### 6. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Course content section cards in an online university platform. Rounded white card container (16px radius) with subtle slate border and soft ambient shadow, bold section header title 'Topic 3: Neural Networks & Deep Learning', progress pill badge '4/6 Completed', collapsible accordion toggle arrow, light gray interior card container listing lesson modules. Clean modern edtech UI, light mode."*

---

### Zone 7: Course Activity Cards & Modules (`.activity-item`)

#### 1. Visual Purpose & Context
The single most important UI component in Moodle. Represents each learning element: Assignment, Quiz, Forum, Page, File, URL, H5P interactive video, etc.

#### 2. Why Default Boost Looks "Pale"
- Activities use muted colors with faint borders.
- The 2D CSS grid looks squished and lacks visual hierarchy between the title, due dates, completion pill, and icon.

#### 3. Exact Source Locations
- **HTML Template**: `moodle/public/course/format/templates/local/content/cm/item.mustache` & `activity.mustache`
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/course.scss` (lines 280-450)
- **Activity Icon Colors**: `moodle/public/theme/boost/scss/moodle/icons.scss` (lines 195-255) & `variables.scss`

#### 4. DOM Tree & Selector Hierarchy
```
li.activity[data-for="cmitem"]
 └── div.activity-item (drag: .dragging, hidden: .hiddenactivity)
      └── div.activity-grid
           ├── div.activity-icon-area
           │    └── div.activityiconcontainer (categories: .assessment, .collaboration, etc.)
           │         └── img.activityicon
           ├── div.activity-name-area
           │    └── div.activityname > a.aalink
           ├── div.activity-completion-area
           │    └── button[data-action="toggle-manual-completion"] OR span.badge
           ├── div.activity-actions
           │    └── div.action-menu
           ├── div.activity-altcontent.activity-description
           └── div.activity-dates
```

#### 5. Activity Category Color Matrix (`scsspre`)
Moodle dynamically colors activity icons by category. Set these in `scsspre`:
```scss
$activity-icon-administration-bg:     #a855f7; // Purple (Badges, Admin)
$activity-icon-assessment-bg:         #ec4899; // Rose Pink (Quizzes, Assignments)
$activity-icon-collaboration-bg:      #6366f1; // Indigo (Forums, Wikis, Workshops)
$activity-icon-communication-bg:      #f97316; // Orange (Feedback, Chat, Choice)
$activity-icon-content-bg:            #06b6d4; // Cyan (Files, Folders, Books, URLs)
$activity-icon-interactivecontent-bg: #8b5cf6; // Violet (H5P, SCORM, Lessons)
```

#### 6. Custom CSS/SCSS Overrides (`scss`)
```scss
.activity-item {
    background-color: #ffffff !important;
    border: 1px solid #e2e8f0 !important;
    border-radius: 0.75rem !important;
    padding: 1rem 1.25rem !important;
    margin-bottom: 0.75rem;
    box-shadow: 0 1px 3px rgba(15, 23, 42, 0.03);
    transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);

    &:hover {
        border-color: #cbd5e1 !important;
        box-shadow: 0 6px 14px -2px rgba(15, 23, 42, 0.07);
        transform: translateY(-1.5px);
    }

    .activityiconcontainer {
        width: 44px;
        height: 44px;
        border-radius: 0.625rem;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.06);
    }

    .activityname {
        font-weight: 600;
        font-size: 0.95rem;

        .aalink {
            color: #0f172a;
            &:hover {
                color: $primary;
                text-decoration: none;
            }
        }
    }

    .activity-dates {
        font-size: 0.8rem;
        color: #64748b;
        margin-top: 0.35rem;
    }

    // Completion Badge Pill
    .activity-completion-area button {
        border-radius: 2rem;
        font-size: 0.78rem;
        font-weight: 600;
        padding: 0.3rem 0.85rem;
    }
}
```

#### 7. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Collection of modern course activity list cards in an educational LMS web app. Card 1: Quiz activity with vibrant rose-pink rounded square icon containing a pencil emblem, title 'Midterm Quiz: Algorithms', deadline pill 'Due Friday, 11:59 PM', green pill button 'Completed'. Card 2: Discussion forum with indigo square icon, title 'Weekly Discussion Forum', badge '3 Unread Posts'. White card background, 12px rounded corners, subtle border, smooth hover elevation. Dribbble top UI post, clean aesthetic."*

---

### Zone 8: Dashboard & My Courses Overview (`.block_myoverview`)

#### 1. Visual Purpose & Context
The student and teacher dashboard landing page. Houses course cards with banner images, progress bars, starred badges, and action menus.

#### 2. Why Default Boost Looks "Pale"
- Cards have flat borders, low-contrast progress bars, and harsh squared image tops.

#### 3. Exact Source Locations
- **HTML Template**: `moodle/public/course/templates/coursecard.mustache` & `coursecards.mustache`
- **Block Template**: `moodle/public/blocks/myoverview/templates/main.mustache`
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/blocks.scss` & `moodle/public/theme/boost/scss/moodle/courseoverview.scss`

#### 4. DOM Tree & Selector Hierarchy
```
div.block_myoverview
 └── div.dashboard-card-deck.coursecards
      └── div.card.course-card[data-region="course-content"]
           ├── a > div.card-img-top (background image)
           ├── div.card-body.course-info-container
           │    ├── div.text-muted.coursecategory
           │    ├── a.aalink.coursename
           │    └── div.badge (e.g. Hidden from students)
           └── div.card-footer
                ├── div.progress > div.progress-bar
                └── div.card-footer.menu > div.course-action-menu
```

#### 5. Custom CSS/SCSS Overrides (`scss`)
```scss
.block_myoverview .course-card {
    border: 1px solid #e2e8f0;
    border-radius: 1rem;
    overflow: hidden;
    background: #ffffff;
    box-shadow: 0 2px 4px rgba(15, 23, 42, 0.04);
    transition: all 0.25s ease;

    &:hover {
        transform: translateY(-4px);
        box-shadow: 0 12px 24px -4px rgba(15, 23, 42, 0.1);
        border-color: #cbd5e1;
    }

    .card-img-top {
        height: 140px;
        background-size: cover;
        background-position: center;
        position: relative;

        &::after {
            content: '';
            position: absolute;
            inset: 0;
            background: linear-gradient(180deg, rgba(0,0,0,0) 60%, rgba(0,0,0,0.4) 100%);
        }
    }

    .course-info-container {
        padding: 1.25rem;

        .coursename {
            font-size: 1rem;
            font-weight: 700;
            color: #0f172a;
        }

        .coursecategory {
            font-size: 0.8rem;
            color: #64748b;
            text-transform: uppercase;
            font-weight: 600;
        }
    }

    .progress {
        height: 6px;
        border-radius: 10px;
        background-color: #e2e8f0;

        .progress-bar {
            border-radius: 10px;
            background: linear-gradient(90deg, $primary, #06b6d4);
        }
    }
}
```

#### 6. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Grid of modern course cards on a clean dashboard page. Each card has a colorful 16:9 hero thumbnail photo (coding, design, biology), category badge pill on top, bold course title 'UX/UI Masterclass 2026', sleek dual-color gradient progress bar (78% Complete), instructor thumbnail circle, and a minimalist three-dots menu. Card surfaces are crisp white with soft floating elevation on light slate background."*

---

### Zone 9: Modern Split Login Page (`body.pagelayout-login`)

#### 1. Visual Purpose & Context
The authentication portal. Moodle 5.x uses a modern split screen: the left half is a photographic / hero branded panel, and the right half contains the username, password, SSO buttons, and instructions.

#### 2. Why Default Boost Looks "Pale"
- Left panel defaults to flat blue or an un-styled AI image watermark.
- Right panel form fields lack floating icons, padding, or refined focus rings.

#### 3. Exact Source Locations
- **HTML Template**: `moodle/public/theme/boost/templates/login.mustache`
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/login.scss`
- **Settings**: `theme_boost | loginbackgroundimage` (in `settings.php`)

#### 4. DOM Tree & Selector Hierarchy
```
body.pagelayout-login
 └── div#page.login-layout
      ├── div.login-layout-left (hero side)
      │    ├── div.login-layout-left-content
      │    │    ├── blockquote
      │    │    └── a
      │    └── div.login-layout-stats > div.login-layout-stat
      └── div.login-layout-right (form side)
           └── div.login-layout-right-content > div.login-container
                ├── div.login-form
                │    ├── div.login-input-wrapper
                │    │    ├── input#username.form-control
                │    │    └── span.login-input-icon
                │    ├── div.login-input-wrapper
                │    │    ├── input#password.form-control
                │    │    ├── span.login-input-icon
                │    │    └── button.toggle-sensitive-btn
                │    └── div.login-form-submit > button.btn.btn-primary
                ├── div.login-separator
                └── div.login-identityproviders (SSO)
```

#### 5. Custom CSS/SCSS Overrides (`scss`)
```scss
body.pagelayout-login {
    .login-layout-left {
        background: linear-gradient(135deg, #1e1b4b 0%, #312e81 50%, #4338ca 100%);
        position: relative;
        overflow: hidden;

        &::before {
            content: '';
            position: absolute;
            width: 600px;
            height: 600px;
            border-radius: 50%;
            background: radial-gradient(circle, rgba(99, 102, 241, 0.3) 0%, transparent 70%);
            top: -100px;
            right: -100px;
        }

        .login-layout-left-content {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.15);
            border-radius: 1.25rem;
            color: #ffffff;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.2);
        }
    }

    .login-layout-right {
        background-color: #ffffff;

        .login-input-wrapper .form-control {
            border: 1px solid #e2e8f0;
            background-color: #f8fafc;
            border-radius: 0.75rem;
            padding: 0.75rem 1rem 0.75rem 3rem;

            &:focus {
                border-color: $primary;
                background-color: #ffffff;
                box-shadow: 0 0 0 3px rgba($primary, 0.15);
            }
        }

        .login-form-submit .btn {
            border-radius: 0.75rem;
            padding: 0.75rem;
            font-weight: 600;
            background: linear-gradient(135deg, $primary 0%, darken($primary, 8%) 100%);
            border: none;
            box-shadow: 0 4px 12px rgba($primary, 0.3);
        }
    }
}
```

#### 6. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Split-screen login UI for a premium university LMS. Left half: rich deep indigo and midnight-violet abstract geometric mesh gradient background with translucent glassmorphic quote card showing student testimonial and stats ('98,000+ Students Enrolled'). Right half: pristine white form column featuring minimalist brand logo, greeting headline 'Welcome back', clean floating email and password inputs with subtle icons, indigo 'Sign in' gradient button with soft glow, and SSO buttons for Google and Microsoft. Figma mockup, 8k resolution."*

---

### Zone 10: Global Form Elements, Buttons & Modals

#### 1. Visual Purpose & Context
Foundational UI primitives used everywhere: input fields, selects, checkboxes, primary/secondary action buttons, pop-up confirmation modals, and floating notification toasts.

#### 2. Exact Source Locations
- **Buttons SCSS**: `moodle/public/theme/boost/scss/moodle/buttons.scss`
- **Forms SCSS**: `moodle/public/theme/boost/scss/moodle/forms.scss`
- **Modal SCSS**: `moodle/public/theme/boost/scss/moodle/modal.scss`
- **Toasts SCSS**: `moodle/public/theme/boost/scss/moodle/toasts.scss`

#### 3. Custom CSS/SCSS Overrides (`scss`)
```scss
// Buttons
.btn-primary {
    background: linear-gradient(135deg, $primary 0%, darken($primary, 8%) 100%);
    border: none;
    box-shadow: 0 2px 6px rgba($primary, 0.25);
    border-radius: 0.625rem;
    font-weight: 600;
    transition: all 0.2s ease;

    &:hover, &:focus {
        background: linear-gradient(135deg, lighten($primary, 4%) 0%, $primary 100%);
        box-shadow: 0 4px 12px rgba($primary, 0.35);
        transform: translateY(-1px);
    }
}

.btn-secondary {
    background-color: #f1f5f9;
    border: 1px solid #e2e8f0;
    color: #334155;
    border-radius: 0.625rem;
    font-weight: 500;

    &:hover {
        background-color: #e2e8f0;
        color: #0f172a;
    }
}

// Form Controls
.form-control, .form-select {
    border-radius: 0.625rem;
    border: 1px solid #cbd5e1;
    padding: 0.6rem 0.85rem;
    transition: border-color 0.15s ease, box-shadow 0.15s ease;

    &:focus {
        border-color: $primary;
        box-shadow: 0 0 0 3px rgba($primary, 0.15);
    }
}

// Modals
.modal-content {
    border: 1px solid #e2e8f0;
    border-radius: 1.25rem;
    box-shadow: 0 25px 50px -12px rgba(15, 23, 42, 0.25);
    overflow: hidden;

    .modal-header {
        border-bottom: 1px solid #f1f5f9;
        background-color: #f8fafc;
        padding: 1.25rem 1.5rem;
    }

    .modal-footer {
        border-top: 1px solid #f1f5f9;
        background-color: #f8fafc;
        padding: 1rem 1.5rem;
    }
}

// Floating Toast Notifications
#toast-wrapper .toast {
    border: 1px solid #e2e8f0;
    border-radius: 0.875rem;
    box-shadow: 0 10px 25px -5px rgba(15, 23, 42, 0.1);
    backdrop-filter: blur(8px);
}
```

#### 4. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"UI design system component sheet: Modern rounded primary button with violet gradient and soft shadow, elegant secondary button, sleek text input field with active focus border, pop-up confirmation modal with rounded corners and frosted backdrop, floating notification toast card with green success checkmark on light slate background. Polished Figma design tokens library."*

---

### Zone 11: Main Footer & Sticky Footer (`footer#page-footer`, `.stickyfooter`)

#### 1. Visual Purpose & Context
The base footer (copyright, language selector, moodle documentation, app link) and the floating sticky action footer (shown during assignment grading or editing).

#### 2. Exact Source Locations
- **HTML Template**: `moodle/public/theme/boost/templates/footer.mustache`
- **Footer SCSS**: `moodle/public/theme/boost/scss/moodle/layout.scss`
- **Sticky Footer SCSS**: `moodle/public/theme/boost/scss/moodle/sticky-footer.scss`

#### 3. Custom CSS/SCSS Overrides (`scss`)
```scss
footer#page-footer {
    background-color: #0f172a;
    color: #94a3b8;
    border-top: 1px solid #1e293b;
    padding: 3rem 0;
    margin-top: 4rem;

    a {
        color: #cbd5e1;
        transition: color 0.15s ease;
        &:hover { color: #ffffff; text-decoration: none; }
    }

    .footer-container {
        max-width: 1200px;
        margin: 0 auto;
    }
}

.stickyfooter {
    background: rgba(255, 255, 255, 0.9) !important;
    backdrop-filter: blur(12px);
    border-top: 1px solid #e2e8f0;
    box-shadow: 0 -4px 16px rgba(15, 23, 42, 0.05);
}
```

#### 4. Gemini Argon Prompt Reference
> **Gemini Argon Prompt**:  
> *"Modern dark slate web app footer. Deep charcoal slate (#0f172a) background, clean 4-column link layout (About, Resources, Community, Support), subtle copyright text, mobile store badge pills, smooth muted gray typography with white hover states."*

---

### Zone 12: Calendar & Upcoming Events (`.calendarwrapper`, `.block_calendar_month`)

#### 1. Visual Purpose & Context
The calendar views, monthly tables, date chip badges, and upcoming activity deadlines.

#### 2. Exact Source Locations
- **SCSS Stylesheet**: `moodle/public/theme/boost/scss/moodle/calendar.scss`
- **Template**: `moodle/public/calendar/templates/`

#### 3. Custom CSS/SCSS Overrides (`scss`)
```scss
.calendarwrapper table.calendartable {
    border: none;

    th {
        font-weight: 700;
        color: #64748b;
        text-transform: uppercase;
        font-size: 0.75rem;
    }

    td {
        border: 1px solid #f1f5f9;
        border-radius: 0.5rem;
        transition: background-color 0.15s ease;

        &.today {
            background-color: rgba($primary, 0.08);
            font-weight: 700;
            color: $primary;
        }

        &:hover {
            background-color: #f8fafc;
        }
    }

    .calendar-event {
        border-radius: 0.35rem;
        padding: 2px 6px;
        font-size: 0.75rem;
        font-weight: 500;
    }
}
```

---

## 4. Dark Mode Architecture (`theme_boost/colour-mode.scss` & `dark.scss`)

Moodle 5.x Boost has built-in color-mode switching.
- **Enabled via**: Site Administration ➔ Appearance ➔ Themes ➔ Boost ➔ Experimental settings ➔ `Enable colour modes` (`theme_boost/enablecolourmodes`).
- **CSS Trigger**: When dark mode is active, Moodle sets `[data-bs-theme="dark"]` on the root or `body`.
- **How to target dark mode in `theme_boost | scss`**:
```scss
// Standard Bootstrap 5 theme selector
[data-bs-theme="dark"] {
    --bs-body-bg: #090d16;
    --bs-body-color: #f1f5f9;
    --bs-card-bg: #111827;
    --bs-border-color: #1f2937;

    .navbar.fixed-top {
        background: rgba(17, 24, 39, 0.85) !important;
        border-bottom-color: #1f2937 !important;
    }

    .activity-item {
        background-color: #111827 !important;
        border-color: #1f2937 !important;
        &:hover {
            border-color: #374151 !important;
            box-shadow: 0 6px 14px -2px rgba(0, 0, 0, 0.4);
        }
    }

    .course-section .section-item {
        background-color: #111827;
        border-color: #1f2937;
        .course-section-header { background-color: #111827; }
        .section-content-inner { background-color: #0f172a; border-top-color: #1f2937; }
    }
}
```

---

## 5. Instructions for Gemini Argon

When feeding this file to Gemini Argon to generate CSS and mockups, follow these guidelines:

1. **Strictly Separate Variables from Rules**:
   - Variables (colors, spacing, font stacks, drawer dimensions) must be generated for **`theme_boost | scsspre`**.
   - Rules, classes, pseudo-selectors, and media queries must be generated for **`theme_boost | scss`**.
2. **Preserve Moodle Layout Mechanics**:
   - Never set `position: absolute` or change `display: flex` on `.drawers`, `#page`, `#region-main`, or `.navbar.fixed-top` as this breaks Moodle's responsive drawer anchoring and mobile sliding drawers.
   - Always use `.activity-grid` for activity inner layouts.
3. **Use CSS Custom Property Fallbacks**:
   - Moodle uses both Bootstrap `--bs-*` and Moodle Design System `--mds-*` properties. Always write rules that cleanly override or leverage these variables.
4. **Elevation and Contrast Standards**:
   - Replace Moodle's default pale gray (`#dee2e6`, `#e9ecef`) with modern Slate tokens:
     - Backgrounds: `#f8fafc` (Light) / `#090d16` (Dark)
     - Surfaces: `#ffffff` (Light) / `#111827` (Dark)
     - Borders: `#e2e8f0` (Light) / `#1f2937` (Dark)
     - Text Primary: `#0f172a` (Light) / `#f8fafc` (Dark)
     - Text Muted: `#64748b` (Light) / `#94a3b8` (Dark)
