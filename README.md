# Forkify 🍴

A modern, single-page recipe application that lets you search hundreds of thousands of recipes, adjust serving sizes, bookmark your favourites, and share your own creations with the world.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Usage Guide](#usage-guide)
- [API Reference](#api-reference)
- [Known Limitations](#known-limitations)
- [Roadmap](#roadmap)

---

## Features

**Search** — Query over 1,000,000 recipes from the Forkify API. Results are paginated at 10 per page for a clean, readable layout.

**Recipe View** — Click any result to see the full recipe: ingredients, cooking time, serving count, and a direct link to the original source for step-by-step directions.

**Dynamic Servings** — Use the `+` and `−` buttons to scale the recipe up or down. Every ingredient quantity recalculates automatically in real time.

**Bookmarks** — Save recipes you love. Bookmarks are stored in your browser's `localStorage` so they persist between sessions without needing a user account.

**Upload Your Own Recipe** — Submit a recipe via the built-in form. Uploaded recipes are sent to the API, automatically bookmarked, and immediately visible in your session.

---

## Tech Stack

Forkify is written in **vanilla JavaScript (ES2022)** and uses no UI framework. The key tools and libraries are listed below.

| Tool / Library                                                 | Purpose                                                                  |
| -------------------------------------------------------------- | ------------------------------------------------------------------------ |
| [Parcel 2](https://parceljs.org/)                              | Zero-config bundler and dev server                                       |
| [core-js](https://github.com/zloirock/core-js)                 | Polyfills for older browser support                                      |
| [regenerator-runtime](https://github.com/facebook/regenerator) | Enables `async/await` in all browsers                                    |
| [Fraction.js](https://github.com/rawify/Fraction.js)           | Renders ingredient quantities as readable fractions (e.g. `0.5` → `1/2`) |
| [Forkify API v2](https://forkify-api.jonas.io)                 | Remote recipe data source                                                |

---

## Architecture

Forkify follows the **MVC (Model-View-Controller)** pattern, which separates the application into three distinct responsibilities.

```
┌─────────────────────────────────────────────────┐
│                  controller.js                  │
│   (Orchestrates everything — the "conductor")   │
└──────────────┬──────────────────┬───────────────┘
               │                  │
               ▼                  ▼
┌──────────────────┐   ┌─────────────────────────┐
│    model.js       │   │       View classes       │
│                  │   │                         │
│  · state object  │   │  recipeView.js          │
│  · loadRecipe    │   │  searchView.js          │
│  · loadSearch    │   │  resultsView.js         │
│  · updateServings│   │  paginationView.js      │
│  · addBookmark   │   │  bookmarksView.js       │
│  · uploadRecipe  │   │  addRecipeView.js       │
│                  │   │  previewView.js         │
└──────────────────┘   └─────────────────────────┘
```

The **model** is the single source of truth for all application data. It fetches from the API and stores everything in a central `state` object. The **views** are responsible only for rendering HTML — they never touch data directly. The **controller** sits in the middle: it listens for user events (via the views) and responds by calling model functions and triggering re-renders.

---

## Project Structure

```
forkify/
├── src/
│   ├── img/
│   │   └── icons.svg
│   ├── js/
│   │   ├── config.js          # App-wide constants (API URL, timeouts, keys)
│   │   ├── helpers.js         # AJAX utility with timeout race
│   │   ├── model.js           # All data logic and state management
│   │   ├── controller.js      # Event handlers and MVC wiring
│   │   └── views/
│   │       ├── View.js        # Base class inherited by all views
│   │       ├── recipeView.js
│   │       ├── searchView.js
│   │       ├── resultsView.js
│   │       ├── paginationView.js
│   │       ├── bookmarksView.js
│   │       ├── addRecipeView.js
│   │       └── previewView.js
│   └── sass/
│       └── ...                # Stylesheets
├── index.html
├── package.json
└── README.md
```

---

## Getting Started

### Prerequisites

You will need [Node.js](https://nodejs.org/) (v16 or higher) and npm installed on your machine.

### Installation

**1.** Clone the repository.

```bash
git clone https://github.com/yourusername/forkify.git
cd forkify
```

**2.** Install dependencies.

```bash
npm install
```

**3.** Start the development server.

```bash
npm start
```

Parcel will open the app at `http://localhost:1234` and watch your files for changes.

### Building for Production

```bash
npm run build
```

The optimised output will be placed in the `dist/` folder, ready to deploy to any static hosting service such as Netlify, Vercel, or GitHub Pages.

---

## Configuration

All configurable values live in `src/js/config.js`. You should not need to change most of these, but the table below explains each one.

| Constant          | Default                                        | Description                                                |
| ----------------- | ---------------------------------------------- | ---------------------------------------------------------- |
| `API_URL`         | `https://forkify-api.jonas.io/api/v2/recipes/` | Base URL for all API requests                              |
| `TIMEOUT_SEC`     | `10`                                           | Seconds before a network request is abandoned              |
| `RES_PER_PAGE`    | `10`                                           | Number of search results shown per page                    |
| `KEY`             | _(your key)_                                   | Your personal API key — see below                          |
| `MODAL_CLOSE_SEC` | `2.5`                                          | Delay in seconds before the upload modal closes on success |

### Getting an API Key

This app uses the [Forkify API](https://forkify-api.jonas.io). To upload your own recipes, you need a free API key.

1. Visit [https://forkify-api.jonas.io](https://forkify-api.jonas.io) and generate your key.
2. Replace the `KEY` value in `config.js` with your own.

> ⚠️ **Important:** Never commit your real API key to a public repository. Consider moving it to a `.env` file and adding that file to `.gitignore`.

---

## Usage Guide

**Searching for a recipe** — Type any ingredient or dish name into the search bar and press Enter. Results appear in the left panel, 10 at a time.

**Viewing a recipe** — Click any search result. The full recipe loads in the main panel. The URL updates with the recipe's ID (e.g. `#5ed6604591c37cdc054bc886`), so you can share or bookmark the link directly in your browser.

**Adjusting servings** — Use the `−` and `+` buttons next to the serving count. All ingredient quantities update instantly.

**Bookmarking** — Click the bookmark icon (🔖) on any recipe to save it. Your bookmarks appear in the top-right panel and are preserved when you close and reopen the browser.

**Uploading a recipe** — Click the **+ Add Recipe** button in the navigation bar. Fill in the form fields. Ingredients must be entered one per field in the exact format:

```
quantity, unit, description
```

For example: `2, cups, plain flour` or `1, tsp, salt`. If a quantity or unit does not apply, leave that field blank but keep the commas: `, , a pinch of pepper`.

---

## API Reference

Forkify communicates with the **Forkify API v2**. All requests are made through the `AJAX` function in `helpers.js`, which automatically handles timeouts and error responses.

| Action            | Method | Endpoint                             |
| ----------------- | ------ | ------------------------------------ |
| Search recipes    | GET    | `{API_URL}?search={query}&key={KEY}` |
| Get single recipe | GET    | `{API_URL}{id}?key={KEY}`            |
| Upload a recipe   | POST   | `{API_URL}?key={KEY}`                |

The API returns JSON. Uploaded recipes are associated with your API key and visible only in sessions that use the same key.

---

## Known Limitations

**Ingredient parsing is strict.** The upload form expects ingredients in `quantity,unit,description` format. Free-form entries (e.g. "a handful of flour") will throw an error.

**No user authentication.** Bookmarks are saved per-browser in `localStorage`. If you clear your browser storage or switch devices, bookmarks will be lost.

**Null ingredient quantities.** Ingredients listed as "to taste" (where quantity is `null`) may display as `0` when serving counts are adjusted, rather than remaining blank.

**No offline support.** The app requires a network connection to fetch recipes. There is no service worker or caching layer.

---

## Roadmap

- [ ] Move API key to environment variables
- [ ] Add a service worker for offline access
- [ ] Fix null quantity behaviour when scaling servings
- [ ] Add ingredient validation with a friendlier error UI
- [ ] Support importing bookmarks across devices via account sync

---
