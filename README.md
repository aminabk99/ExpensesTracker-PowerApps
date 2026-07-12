<div align="center">

#  Expense Tracker
### Enterprise Expense Management — Built on Microsoft Power Platform

A fully functional business expense tracking app built with **Microsoft Power Apps** and **Dataverse**, enabling employees to submit, review, and manage expenses from a single mobile-first interface. Features live spending dashboards, dynamic theming, and enterprise-grade data architecture.

![Power Apps](https://img.shields.io/badge/Power_Apps-Canvas_App-742774?style=for-the-badge&logo=microsoftpowerapps&logoColor=white)
![Dataverse](https://img.shields.io/badge/Dataverse-Backend-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![PowerFx](https://img.shields.io/badge/PowerFx-App_Logic-5C2D91?style=for-the-badge&logo=microsoft&logoColor=white)
![Office 365](https://img.shields.io/badge/Office_365-User_Identity-D83B01?style=for-the-badge&logo=microsoftoffice&logoColor=white)

</div>

---

## How It Works

1. **Launch** — the Loading Screen initializes the global theming system (`gblAppColors`, `gblAppStyles`) and loads the user's Office 365 identity and Teams context before navigating to the Home Screen
2. **Dashboard** — employees see their total spend, a monthly trend line chart, quick-fill templates for common expense types, and their 5 most recent submissions with color-coded approval status
3. **Submit** — employees fill out the Add Expense form (description, date, category, amount, currency) and hit Submit — the app calls `Patch()` to write to Dataverse, confirms success, and resets the form
4. **Review** — the Overview Screen lists every expense the logged-in user has submitted, sorted by date, with a running total at the top
5. **Stay compliant** — the Policy Screen shows the full Galactic Corp Expense Policy in a scrollable view, accessible from any screen via the bottom nav bar

---

## Screens

| Screen | What It Does |
|---|---|
| **Loading Screen** | Initializes global color/style variables and Teams context; auto-navigates to Home |
| **Home Screen** | Dashboard — total spend, monthly line chart, quick templates, recent expenses |
| **Add Expense Screen** | Expense submission form with validation, `Patch()` write, and success/error feedback |
| **Overview Expense Screen** | Full expense history sorted by date, with running total summary |
| **Policy Screen** | Scrollable company expense policy document |

---

## Data Architecture

Backed by a custom **Dataverse** table with a structured schema and per-user data filtering via `User().Email`:

| Column | Type | Description |
|---|---|---|
| `Description` | Single line of text | Expense description |
| `Date` | Date only | Date the expense was incurred |
| `Total` | Decimal number | Expense amount |
| `Category` | Choice | Travel, Internet, Lunch, Other |
| `Currency` | Choice | USD, EUR, GBP |
| `Status` | Choice | Pending, Approved, Declined |
| `PersonEmail` | Single line of text | Submitting employee's email |
| `PersonName` | Single line of text | Submitting employee's full name |

All reads are filtered by `User().Email` so each employee sees only their own data. All writes use `Patch()` with `Errors()` checked post-write to drive success/failure feedback without losing form data.

---

## Theming System

The app supports **three themes** — Default (light), Dark, and High Contrast — driven by a global architecture initialized on app load:

- **`gblAppColors`** — a full color palette with three variants per color (default, dark, high contrast)
- **`gblAppStyles`** — a resolver that selects the correct variant based on the active `theme` URL parameter

Every control references `gblAppStyles` rather than hardcoded values, so the entire app's appearance changes with a single URL parameter — no individual control edits needed.

---

## Navigation Architecture

Navigation uses a **horizontal gallery-based nav bar** that repeats across all screens:

```powerfx
Table(
    { Title: "Home",     Screen: 'Home Screen' },
    { Title: "Expenses", Screen: 'Overview Expense Screen' },
    { Title: "Add",      Screen: 'Add Expense Screen' },
    { Title: "Policy",   Screen: 'Policy Screen' },
    { Title: "Settings", Screen: App.ActiveScreen }
)
```

The **Add** tab renders as a prominent green circular "+" button via conditional visibility logic (`ThisItem.Title = "Add"`). All other tabs navigate with `Navigate(ThisItem.Screen)`.

---

## How to Import and Run

1. Go to [make.powerapps.com](https://make.powerapps.com)
2. Make sure you have a **Developer environment** with Dataverse enabled
3. Navigate to **Apps → Import canvas app**
4. Upload `ExpensesTrackerApp.zip`
5. Follow the import wizard — select **Create as new** for the app resource
6. Once imported, open in Power Apps Studio, go to **Add data**, and connect your **Expense** Dataverse table (recreate it using the schema above)
7. Save and publish

> **Note:** Requires an Office 365 / Microsoft 365 account with Dataverse access. A free [Microsoft Developer account](https://developer.microsoft.com/en-us/microsoft-365/dev-program) works for testing.

---

## Project Structure

```
ExpensesTracker-PowerApps/
│
└── ExpensesTrackerApp.zip    # Exported Power Apps canvas app package
```

---

## Stack

| Layer | Tech |
|---|---|
| App platform | Microsoft Power Apps (Canvas) |
| Backend | Microsoft Dataverse |
| App logic | PowerFx (`Patch`, `Filter`, `GroupBy`, `AddColumns`, `Switch`, `Concurrent`, `Navigate`) |
| User identity | Office 365 Users connector (`User().Email`, `User().FullName`) |
| Theming | Global variable architecture (`gblAppColors`, `gblAppStyles`) |
| Packaging | Microsoft Power Platform solution export |

---

## Hardest Part

**Building a theming system that scales across every screen without hardcoded values.** Power Apps controls default to inline color properties, making multi-theme support a maintenance nightmare at scale. The solution was initializing two global records on app load — `gblAppColors` with three variants per color, and `gblAppStyles` as a resolver — so every control references a variable rather than a hex value. Switching the entire app's visual appearance requires changing one URL parameter.

## Most Interesting

**The quick-create template system on the Home Screen.** Rather than forcing users to navigate to the Add Expense form and manually select a category, the three most common expense types (Travel, Internet, Lunch) are surfaced as one-tap shortcuts that pre-fill the category field. This mirrors how enterprise productivity apps reduce friction for repetitive actions — and it's built entirely in PowerFx with no custom connectors or code components.

---


<div align="center">
  <sub>Built by <a href="https://github.com/aminabk99">Amina Bilal</a> · <a href="https://linkedin.com/in/amina-bilal-926340382">LinkedIn</a></sub>
</div>
