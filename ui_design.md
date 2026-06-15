# UI Design System Specification
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | Antigravity AI |

---

## 1. Design Philosophy
The user interface design system for Work Index focuses on **Trust, Security, and Professionalism**. Because corporate users interact with statutory and compliance documents, the interface uses a structured, clean, and highly legible layout. High visual contrast, consistent grid spacing, and responsive design ensure an efficient workspace.

---

## 2. Color System
The color palette is built using standard Tailwind CSS colors. It features a slate/gray background for content focus, an Indigo corporate primary, and semantic utility colors.

### 2.1. Brand Colors
- **Primary (Indigo)**: Represents authority and stability.
  - `primary-50`: `#EEF2FF`
  - `primary-600`: `#4F46E5` (Active button state, highlights)
  - `primary-700`: `#4338CA` (Hover state)
- **Secondary (Slate)**: Grounding colors for layouts, cards, and borders.
  - `gray-50`: `#F8FAFC` (Page background)
  - `gray-100`: `#F1F5F9` (Card/Sidebar background)
  - `gray-200`: `#E2E8F0` (Border lines)
  - `gray-700`: `#334155` (Subtext / icons)
  - `gray-900`: `#0F172A` (Main headers / text)

### 2.2. Status Colors
Used for notifications, badge tags, and process warnings:
- **Success (Green)**: `#10B981` (`emerald-500`) — Used for successful uploads, verified files, and active links.
- **Warning (Yellow)**: `#F59E0B` (`amber-500`) — Used for expiring shares or empty folder directories.
- **Danger (Red)**: `#EF4444` (`red-500`) — Used for deletion actions, errors, and revoked team members.
- **Info (Blue)**: `#3B82F6` (`blue-500`) — System messages and general log tracking.

---

## 3. Typography Stack
Typography utilizes highly legible sans-serif typefaces configured as a stack:
- **Font Stack**: `Inter, Outfit, system-ui, -apple-system, sans-serif`
- **Headings**: `font-semibold` / `font-bold` tracking tightly.

### Type Scale
- **H1 (Page Header)**: `24px (1.5rem)`, `leading-8`, `font-bold`
- **H2 (Card / Modal Header)**: `18px (1.125rem)`, `leading-7`, `font-semibold`
- **Body Large (Table Headers)**: `14px (0.875rem)`, `leading-5`, `font-medium`
- **Body Normal (Standard text)**: `14px (0.875rem)`, `leading-5`, `font-normal`
- **Small (Badges, subtext)**: `12px (0.75rem)`, `leading-4`, `font-medium`

---

## 4. Spacing, Borders & Grids
The system is built on a **4px / 8px grid** structure to maintain layout alignment.

- **Padding / Margin**:
  - Small Elements (Badges, small buttons): `px-2 py-1` (8px / 4px)
  - Card Content: `p-6` (24px)
  - Page Content Container: `p-8` (32px)
- **Borders**:
  - Subtle borders: `border border-gray-200`
  - Focus border state: `border-2 border-indigo-600`
- **Border Radius**:
  - Small badges/inputs: `rounded-md` (6px)
  - Cards and Panels: `rounded-lg` (8px)
  - Modals: `rounded-xl` (12px)

---

## 5. UI Component Specifications

### 5.1. Buttons
- **Primary Button**: Indigo base. Text color `#FFFFFF`. Hover state swaps to `primary-700`. Active state utilizes a subtle shadow scaling.
- **Secondary Button**: Bordered gray style. Text color `gray-700`. Border color `gray-200`. Hover state backgrounds to `gray-50`.
- **Danger Button**: Red base. Text color `#FFFFFF`. Hover state triggers a darker red (`red-600`).

### 5.2. File Upload Dropzone
- **Idle State**: Dotted border (`border-2 border-dashed border-gray-300`). Background `#FFFFFF`. Large upload icon in `gray-400`.
- **Drag-Over State**: Dotted border turns `border-indigo-500`. Background shifts to `#EEF2FF` (`primary-50`).
- **Progress Indicator**: Horizontal bar filled in `primary-600` showing percentage progress.

### 5.3. Badges (Financial Year & Roles)
Badges utilize a soft, colored background (10% opacity) combined with highly saturated text for high readability:
- **FY Badge**: Background `#EEF2FF` (10% indigo), Text `#4F46E5`.
- **Admin Role**: Background `#FEF2F2` (10% red), Text `#EF4444`.
- **Editor Role**: Background `#ECFDF5` (10% green), Text `#10B981`.
- **Viewer Role**: Background `#F0F9FF` (10% blue), Text `#0284C7`.

---

## 6. CSS Themes Variables Reference
Custom variables defined in the main CSS bundle:

```css
:root {
  --primary-color: #4f46e5;
  --primary-hover: #4338ca;
  --bg-color: #f8fafc;
  --card-bg: #ffffff;
  --border-color: #e2e8f0;
  --text-main: #0f172a;
  --text-muted: #64748b;
  --success: #10b981;
  --danger: #ef4444;
  --warning: #f59e0b;
}
```
