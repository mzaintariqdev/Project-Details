# 📌 Kanban Task Manager

A modern **Kanban-style task management application** inspired by tools like Trello and Notion.  
It allows users to create boards, organize tasks into columns, and manage work with a smooth drag-and-drop interface.

Can be accessed from here https://smart-task-kanban.netlify.app/

---

## 🚀 Overview

This application allows users to:

- Create and manage multiple **boards** (projects/workspaces)
- Organize tasks inside **Kanban columns**
- Add, edit, and delete tasks
- Drag & drop tasks between columns
- Manage board structure dynamically
- Search and paginate boards
- Persist data across browser sessions

---

## 🧠 Tech Stack

### 🎨 Frontend

- Angular – Standalone component architecture
- Angular Signals – Reactive state management
- Angular Material – UI components (dialogs, buttons, icons)
- Angular CDK – Drag & Drop functionality
- TypeScript – Strongly typed logic

---

## ⚙️ State Management

- Custom service-based architecture (`BoardService`, `KanbanService`)
- Angular Signals (`signal`, `computed`)
- LocalStorage for data persistence

---

## 💾 Data Storage

The application currently uses **browser LocalStorage** to store:

- Boards
- Columns
- Tasks

This ensures data remains available even after page refresh.

---

## 🎯 Features

- 📋 Multiple Kanban boards
- 🧩 Dynamic column management
- ✅ Full CRUD for tasks
- 🔀 Drag & drop task movement
- 🔍 Search boards
- 📄 Pagination
- 💾 Persistent local storage
- 📱 Responsive UI

---

## 📌 Project Goal

A clean and interactive **Kanban task management system** built using Angular, demonstrating modern frontend architecture, reusable components, and reactive state management.

---

## 🚀 Status

- Frontend application fully functional
