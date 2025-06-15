# Contribution Guide

This document outlines the coding standards and practices for contributions to the project. Note that the backend and frontend have distinct standards due to the different stacks being used.

---

## Code Standards

### Backend
We follow the predefined standards set by AdonisJS:

- **File and Folder Naming:** Use `snake_case`.
- **Architecture:** Stick to the default structure provided by AdonisJS. Use AdonisJS commands for consistency: [AdonisJS Commands](https://docs.adonisjs.com/guides/references/commands).
- **Line Length:** Maximum of 120 characters per line.
- **Quotes:** Use double quotes.
- **Import Organization:**
  1. Adonis package imports
  2. Internal file imports
  3. External packages

  Example:
  ``` javascript
  import { HttpContext } from "@adonis/core";
  import UserModel from "#models/user";
  import lodash from "lodash";
  ```

- **Responses in Controllers:** Use AdonisJS responses for final returns or errors (e.g., `response.ok`, `response.badRequest`, `response.created`).

- **Validators:** Prioritize validators for handling request data.

- **Endpoint Naming:** Follow RESTful API naming conventions: [Resource Naming](https://restfulapi.net/resource-naming/).

- **Testing:** Create a folder per controller, and a file per endpoint. For example, `Project/create` tests the `createProject` request in the `Project` controller.

- **New Packages:** Verify compatibility with AdonisJS 6 before installation: [AdonisJS Packages](https://packages.adonisjs.com/).

- **Shared Types:** Centralize shared types in the root project’s shared library for cross-compatibility between frontend and backend. For example, create a type for the `User` controller inspired by its model in the shared library for frontend access.

---

### Frontend
We adhere to Next.js standards:

- **Component and File Naming:**
  - Use `PascalCase` for function components, Storybook files, and style files (`.components`, `.storybook`, `.style.tsx`).
  - Use `camelCase` for other TypeScript files.
- **Quotes:** Use double quotes.
- **Import Organization:**
  1. React package imports
  2. Internal file imports
  3. External packages

  Example:
  ``` javascript
  import React from "react";
  import Button from "@design-system/button";
  import lodash from "lodash";
  ```

- **Design System:** General components, text, and colors are defined in `libs/ui`.

- **Types:** General types are defined in `libs/types`.

- **Constants:** Use `libs/endpoint` for constants (e.g., images, endpoint names).
- **Component Categorization:**
  - Determine a component’s complexity (design and/or logic) and its reusability.
  - Avoid unnecessary code duplication.
- **Domain Folder:** Store components specific to a page and used **only** on that page.
- **Multi-step Forms:** Separate each step into its own component.
- **Sub-components:** Limit to two sub-function components within a file.
- **CSS:**
  - Use the design system for styles, defined in code and on Figma.
  - Ensure component flexibility (e.g., components should default to full-width).
- **Storybook:** Create Storybook files showcasing all possible states/variants of general components, including interactions where applicable.
- **CSS Utilities:**
  - Use `styled` for elements with variants or dynamic styles.
  - Use `css` for static designs.
- **Assets:**
  - Store PNG images in the `public` folder.
  - Import custom SVGs as function components with `width` and `height` props.
- **Internationalization (i18n):**
  - Add English values to `messages/en.json` using the format:
    `Page/Component-Element/Sub-Component/Value`.
    - Example: For `<button><tag>Text</tag></button>` on the homepage, use: `"HomePage/Button/tag: Text"`.
- **Custom Hooks:** Create hooks for reusable logic (e.g., modal handling).
- **Rendering:** Explicitly define client-side or server-side rendering for components.

