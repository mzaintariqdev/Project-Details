# Storybook Demo Project

## Project Overview
This is a **small demo using Storybook** to document and showcase reusable **React UI components**.  
It demonstrates:

- Components: **Button**, **Input**, **Card**  
- Interactive **stories** with `args` and `argTypes`  
- **MDX documentation** with Canvas & Controls  
- Testing different **variants, sizes, and states**  

The goal is to provide a **self-contained component library** for exploring and documenting UI components interactively.

---

## Components

### Button
- Variants: `primary`, `secondary`, `danger`  
- Sizes: `small`, `medium`, `large`  
- Supports `disabled` state  
- Interactive Playground for testing props

  MDX Doc for button
  <img width="1920" height="949" alt="image" src="https://github.com/user-attachments/assets/75bc46e3-29e8-4e18-931f-1fdc11e5f125" />


### Input
- Sizes: `small`, `medium`, `large`  
- Types: `text`, `password`, `email`  
- Controlled input behavior using React state  

### Card
- Variants: `default`, `primary`, `secondary`  
- Optional shadow  
- Can include an image  
- Demonstrates layouts and visual states  

---

Video from the Storybook UI

[screen-capture (9).webm](https://github.com/user-attachments/assets/fff44112-57c0-44b5-9c2f-ef8598014f12)



## Getting Started

### Clone the repository
```bash
git clone https://github.com/mzaintariqdev/storybook.git
cd storybook
npm i
npm run storybook (to run storybook UI)
