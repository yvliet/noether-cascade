# Cascade for Noether

Organize notes into sequential cascades (books) with status-bar navigation, graph backlinks, and custom sidebar folders.

---

## 1. Overview & User Experience

Some notes are meant to be read in sequence, such as book chapters, multi-part tutorials, onboarding manuals, and serial essays. 

**Cascade** allows you to turn related notes into sequential "cascades" or books. It displays previous and next chapter buttons in the bottom status bar, links chapters together in the knowledge graph without cluttering your Markdown text, and adds a dedicated Cascade bookshelf tab to the left sidebar.

### Where It Lives in Noether
- **Left Sidebar Tab**: Click the book cascade icon beside Bookmarks to open your shelves and book series.
- **Status Bar Dock**: When viewing any note that belongs to a cascade, previous and next navigation arrows appear in the bottom dock.
- **Note Sub-Header**: Displays chapter sequence numbers (e.g. Chapter 3 of 12).

## 2. Features & Step-by-Step Guide

### 1. Creating a Cascade (Book)
1. Open the **Cascade** drawer in the left sidebar.
2. Click **New Cascade** and give your book or series a title.
3. Drag and drop notes from the file tree into the cascade list to arrange their reading sequence.

### 2. Reading and Navigating
- When reading a cascade note, look at the bottom status bar:
  - Click `← Previous Chapter` to jump to the preceding note.
  - Click `Next Chapter →` to advance.
- Keyboard shortcuts: Press `Alt+[` for previous chapter and `Alt+]` for next chapter.

## 3. Architecture & SDK Blueprint (For Extension Builders)

Cascade demonstrates how community extensions can maintain linked-list relationships, mount status-bar navigation docks, and register custom sidebar tabs via the Noether SDK.

### SDK Extension Points Used
- `this.registerSidebarTab()`: Mounts the left sidebar bookshelf manager.
- `this.addStatusBarItem()`: Dynamically renders sequential previous/next link pills in the bottom dock.
- `this.addCommand()`: Registers keyboard hotkeys for linear chapter navigation.
- `this.defineTable()`: Declares cascade_books and cascade_items tables in SQLite.

### Real SDK Implementation Pattern

```typescript
import { Extension, NoetherApp } from 'noether';
import React from 'react';

export default class CascadeExtension extends Extension {
  async onload(): Promise<void> {
    // 1. Dynamic Status Bar Chapter Navigation Dock
    this.addStatusBarItem({
      id: 'cascade-nav-dock',
      position: 'right',
      render: (app: NoetherApp) => {
        const activeDoc = app.vault.activeDocument;
        if (!activeDoc) return null;
        return React.createElement(
          'div',
          { className: 'flex items-center gap-2 text-xs text-[#888]' },
          React.createElement('button', { onClick: () => this.navigatePrev(activeDoc.id) }, '← Prev'),
          React.createElement('button', { onClick: () => this.navigateNext(activeDoc.id) }, 'Next →')
        );
      },
    });
  }
}
```

## 4. MCP Tools Reference

### 1. `cascade_list_books`
- **Description**: Lists all defined cascade books and their member documents.
- **Parameters**: None.

### 2. `cascade_add_to_book`
- **Description**: Appends a document to a cascade sequence.
- **Parameters**:
  - `bookId` (string, required): Target cascade book identifier.
  - `documentId` (string, required): Document to append.

## 5. Development & Local Building

To build and test this community extension locally:

```bash
git clone https://github.com/yvliet/noether-cascade.git
cd noether-cascade
npm install
npm run build
```

Copy the compiled bundle `dist/main.js` and `manifest.json` into your vault's `.noether/extensions/noether-cascade/` directory and reload Noether.

## 6. License

MIT © [Yuliet Li](https://github.com/yvliet)
