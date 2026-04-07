# Multi CMS Manager

**Version:** 0.1.0  

A **Next.js application** demonstrating integration of **multiple headless CMS platforms**—**Sanity** and **Contentful**—for CRUD operations. Built as a **learning and practice project**, this app covers topics such as **React Query**, **REST API**, **GraphQL**, and advanced CMS workflows in a single unified interface.

Hosted here: https://multi-cms-liart.vercel.app/

---

## Table of Contents

1. [Project Overview](#project-overview)  
2. [Features](#features)  
3. [Tech Stack](#tech-stack)  
4. [Installation](#installation)  
5. [Environment Variables](#environment-variables)  
6. [How to Run the App](#how-to-run-the-app)  
7. [Project Structure](#project-structure)  
8. [Learning Objectives](#learning-objectives)  
9. [License](#license)  

---

## Project Overview

The purpose of **Multi CMS Manager** is to practice and demonstrate:

- Integration of **Sanity** and **Contentful** in a single application  
- Performing **CRUD operations** on multiple CMS platforms  
- Using **React Query** for efficient data fetching and caching  
- Working with **REST APIs** and **GraphQL queries**  
- Managing dynamic data in **Next.js** with SSR and client-side rendering  

> This project is intended for learning and experimentation rather than production use.

---

## Features

- 📝 **CRUD Operations**: Create, read, update, and delete content on Sanity and Contentful  
- ⚡ **React Query Integration**: Optimized data fetching, caching, and state management  
- 🌐 **Multiple CMS Support**: Unified interface for interacting with different CMSs  
- 🔀 **Next.js Routing**: SSR and client-side rendering with seamless navigation  
- 🛠️ **TypeScript Support**: Fully typed codebase for improved reliability  
- 🎨 **TailwindCSS**: Utility-first styling for rapid UI development  

---

## Tech Stack

- **Frontend**:  
  - Next.js `16.2.2`  
  - React `19.2.4`  
  - TypeScript `5`  
  - TailwindCSS `4`  

- **CMS & Data**:  
  - Sanity CMS (`@sanity/client`)  
  - Contentful (`contentful`, `contentful-management`)  

- **Data Handling**:  
  - React Query (`@tanstack/react-query`)  
  - Axios (`axios`)  
  - GraphQL Request (`graphql-request`)  

- **Dev Tools**:  
  - ESLint (`eslint`, `eslint-config-next`)  
  - Node.js (`>=22.13.0`)  

---

## Installation

1. Clone the repository:

```bash
git clone https://github.com/mzaintariqdev/multi-cms.git
cd multi-cms
```
2. Now move to root directory and run
```bash
npm run dev
