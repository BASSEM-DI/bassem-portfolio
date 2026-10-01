# Bassem Djeddi — Developer Portfolio

A responsive, dark-themed portfolio built for a web development course (Track 2). It introduces the developer, presents six projects, and includes an accessible contact form with inline validation.

## Author

Bassem Djeddi · Developer  
Email: [Bassemdjeddi78@gmail.com](mailto:Bassemdjeddi78@gmail.com)  
LinkedIn: [Bassem Djeddi](https://www.linkedin.com/in/bassem-djeddi-835682360/)

## Tech stack

React (JavaScript), Vite, Tailwind CSS, React Router, and Lucide React.

## Features and requirements

- R1–R2: React and Tailwind app with Home, Projects, Contact, and a fallback route.
- R3: Required fields, trimmed minimum lengths, email validation, accessible inline errors, validation on blur/change/submit, and submission status.
- R4: Mobile-first layouts for 320px, 768px, and 1280px; projects use one, two, and three columns.
- R5: Reusable UI components, project data, pure validators, and a dedicated form hook.
- R6: Production build and ESLint commands for submission checks.
- R7: Setup, project structure, and implementation notes documented here.

The contact form simulates a one-second request, shows success for four seconds, then resets. It does **not** send email or store messages. Use the email or LinkedIn links to contact the author. Project demo/repository URLs intentionally remain `#` placeholders; cards display them as unavailable until real URLs are supplied.

## Setup

Use a Node.js version supported by the installed Vite release (Node 22.12+ recommended).

```sh
npm install
npm run dev
```

```sh
npm run lint
npm run build
npm run preview
```

The development server prints its local URL. The production build is written to `dist/`. When deploying, configure the host to serve `index.html` for unknown paths so React Router can handle direct visits to `/projects` and `/contact`.

## Folder structure

```text
src/
  components/
    layout/           # Existing navigation, footer, and route layout
    portfolio/        # ProjectCard and SkillBadge
    ui/               # Button, Input, Textarea, SectionTitle
  data/projects.js
  hooks/useContactForm.js
  utils/validators.js
  pages/              # Home, Projects, Contact, NotFound
  App.jsx
  main.jsx
  index.css
```

## License

No open-source license has been granted. All rights reserved by Bassem Djeddi; intended for course submission and personal portfolio use.
