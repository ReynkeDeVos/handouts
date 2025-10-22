# VSCode Settings & Setup Guide

This guide provides an overview of my Visual Studio Code extensions and settings.

## Extensions

### Basic/Essential Extensions

- **Live Server:** Launches a local development server with live reload.
- **ESLint:** Integrates JavaScript and TypeScript linting.
- **Prettier - Code formatter:** An opinionated code formatter.
  - Setting: `"editor.defaultFormatter": "esbenp.prettier-vscode"`
  - Setting: `"editor.formatOnSave": true` (Formats code when you save)
- **Path Intellisense:** Autocompletes filenames and paths. (Not sure if still necessary with current VSCode)
- **Auto Rename Tag:** Automatically renames paired HTML/XML tags.
  - **Alternative Setting:** `"editor.linkedEditing": true` (Built-in feature that renames HTML tag pairs with a single edit)
  - Note: Linked editing is currently supported in HTML, XML, and JSX files. You may want to keep the extension for other file types.
- **Better Comments Next:** Improves comment readability with different annotation types.
- **Error Lens:** Enhances the display of errors and warnings.

  - Setting, so that is less bright and more in the background:

  ```json
  "errorLens.decorations": {
      "errorRange": {},
      "warningRange": {},
      "infoRange": {},
      "hintRange": {},
      "errorMessage": { "color": "#ff000080" },
      "warningMessage": { "color": "#ffa50080" },
      "infoMessage": { "color": "#0000ff80" },
      "hintMessage": { "color": "#00800080" }
  }
  ```

- **IntelliCode:** AI-assisted development features.
- **IntelliCode Completions:** Provides weak AI-assisted autocompletion. Not like Copilot.
- **GitHub Pull Requests and Issues:** Manage GitHub PRs and issues.
- **sort-imports** :

  - Or settings for better import management:

  ```json
  {
    "editor.codeActionsOnSave": {
      "source.organizeImports": true
    }
  }
  ```

### Frontend Development Extensions

- **ES7+ React/Redux/React-Native snippets:** Code snippets for React development.
- **HTML CSS Support:** Basic CSS support for HTML documents.
- **Tailwind CSS IntelliSense:** Advanced features for Tailwind CSS.

  - Setting: Associate CSS files with Tailwind:

  ```json
  "files.associations": {
      "*.css": "tailwindcss"
  }
  ```

- **Tailwind Fold:** Improves readability of Tailwind CSS classes.
  - (Toggle folding with `CTRL + ALT + A`)
- **Import Cost:** Displays the size of imported packages.
  - Setting to display it more in the background: `"importCost.fontStyle": "italic"`
- **npm Intellisense:** Autocompletes npm modules in import statements. (Not sure if still necessary)

### Backend Development Extensions

- **REST Client:** Send HTTP requests and view responses within VS Code. (More controll and power with an external application like Insomnia)
- **SonarLint:** Helps detect and fix quality issues in your code. Eats up your RAM. Only install, if more than 16 GB of RAM. Maybe there are better options out there
- **DotENV:** Support for `.env` file syntax.

### Post-Bootcamp / TypeScript Extensions

- **Pretty TypeScript Errors:** Makes TypeScript errors more readable.

## Recommended Editor Settings

### Code Appearance and Navigation

- **Bracket Pair Colorization:**

  - Setting: `"editor.bracketPairColorization.enabled": true` (Colorizes matching brackets)

- **Bracket Pair Guides:**

  - Setting: `"editor.guides.bracketPairs": true` (Shows vertical lines connecting bracket pairs)
  - Setting: `"editor.guides.bracketPairsHorizontal": "active"` (Controls when to render horizontal bracket guides)
  - Setting: `"editor.guides.highlightActiveIndentation": true` (Highlights the active indentation guide)

  You can customize the colors with:

  ```json
  "workbench.colorCustomizations": {
    "editorBracketPairGuide.background1": "#color1",
    "editorBracketPairGuide.activeBackground1": "#color1Bright"
    // Add more colors for background2-6 and activeBackground2-6 if desired
  }
  ```

## Extensions to Be Cautious With (For Learning)

- **GitHub Copilot:** AI pair programmer.
  - **Important Advice for Learners:** It's highly recommended to **disable Copilot completely** while actively learning, as it can prevent you from problem-solving and understanding concepts deeply. You can enable it later once you have a solid foundation of programming skills.

## Common Issues and Solutions

- **ESLint with Tailwind**: If you see "Unknown at rule @tailwind" errors:

  - Ensure CSS files are associated with Tailwind CSS (see settings above)
  - Or add locally to your `.eslintrc` per project:

  ```json
  {
    "rules": {
      "react/no-unescaped-entities": "off"
    }
  }
  ```

### Font Settings (Optional)

- **Editor: Font Family**
  - Install a Nerd Font for coding: [Nerd Fonts](https://www.nerdfonts.com/)
  - My setting: `"'FiraCode Nerd Font','JetBrainsMono NF', 'Consolas', 'Courier New', monospace"`

---

This configuration is based on my setup. Feel free to adjust it to your preferences as you become more comfortable with VS Code.
