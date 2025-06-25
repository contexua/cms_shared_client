# 📦 cms_shared_client

Reusable Vue 3 UI components and utilities for the Contexua CMS ecosystem.

This package provides shared, pre-built components designed to maintain consistency and reduce duplication across Contexua's Vue 3 admin clients.

---

## ✅ Available Components

### **UI Components**

| Component      | Description                         |
| -------------- | ----------------------------------- |
| `UIAlert`      | Styled alert/message box.           |
| `UIButton`     | Primary button component.           |
| `UIEmptyState` | Empty state illustration + message. |
| `UIPilDisplay` | Read-only display of pill/tag.      |
| `UIPilEdit`    | Editable pill/tag component.        |
| `UISpinner`    | Loading spinner indicator.          |
| `UITabs`       | Simple tab navigation.              |

---

### **Modal Components**

| Component           | Description                                          |
| ------------------- | ---------------------------------------------------- |
| `CrudConfirmAction` | Confirmation dialog for destructive actions.         |
| `CrudApplyEdits`    | Modal for applying edits to documents.               |
| `BookmarkDocument`  | Modal to bookmark/save a document.                   |
| `InputAuthCode`     | Modal for entering authentication codes (2FA, etc.). |

---

### **Listing & Search Components**

| Component               | Description                            |
| ----------------------- | -------------------------------------- |
| `CommonFindDocuments`   | Search/filter interface for documents. |
| `CommonListDocuments`   | Standard document list display.        |
| `CommonScrollDocuments` | Infinite scrolling list of documents.  |

---

### **CRUD Components**

| Component                      | Description                                |
| ------------------------------ | ------------------------------------------ |
| `CrudEditDocument`             | Full document editing interface.           |
| `CrudEditDocumentRender`       | Render-based document editing layout.      |
| `CrudEditDocumentRenderCustom` | Custom render option for document editing. |
| `CrudListOptions`              | Options dropdown for document lists.       |
| `CrudIndex`                    | Index view for CRUD operations.            |

---

## 🚀 Installation

In your consuming project:

```bash
npm link cms_shared_client
```
