### FILE: src/data/projects.js
```javascript
export const projects = [
  { id: 1, title: 'Task Manager App', description: 'Create, edit, and track daily tasks with local storage.', tech: ['React', 'Tailwind', 'LocalStorage'], link: '#', repo: '#' },
  { id: 2, title: 'Weather Dashboard', description: 'Fetch live weather data by city using a public API.', tech: ['React', 'API', 'Tailwind'], link: '#', repo: '#' },
  { id: 3, title: 'E-Commerce UI', description: 'A responsive product listing and cart interface.', tech: ['React', 'React Router', 'Tailwind'], link: '#', repo: '#' },
  { id: 4, title: 'Portfolio Website', description: 'This personal portfolio, built to share my work and connect with others.', tech: ['React', 'Vite', 'Tailwind'], link: '#', repo: '#' },
  { id: 5, title: 'Chat Interface', description: 'A minimal chat UI with simulated real-time messages.', tech: ['React', 'Hooks', 'Tailwind'], link: '#', repo: '#' },
  { id: 6, title: 'Movie Search', description: 'Search movies and view details from a public movie API.', tech: ['React', 'API', 'Tailwind'], link: '#', repo: '#' },
]
```

### FILE: src/utils/validators.js
```javascript
const normalize = (value) => String(value ?? '').trim()
export const isRequired = (value) => normalize(value) ? '' : 'This field is required.'
export const minLength = (value, n) => normalize(value).length >= n ? '' : `Please enter at least ${n} characters.`
export const isEmail = (value) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(normalize(value)) ? '' : 'Please enter a valid email address.'
export function validateContactForm(fields) {
  return {
    name: isRequired(fields.name) || minLength(fields.name, 2),
    email: isRequired(fields.email) || isEmail(fields.email),
    subject: isRequired(fields.subject) || minLength(fields.subject, 3),
    message: isRequired(fields.message) || minLength(fields.message, 10),
  }
}
```

### FILE: src/hooks/useContactForm.js
```javascript
import { useEffect, useRef, useState } from 'react'
import { validateContactForm } from '../utils/validators'
const emptyValues = { name: '', email: '', subject: '', message: '' }
const emptyTouched = { name: false, email: false, subject: false, message: false }
export default function useContactForm() {
  const [values, setValues] = useState({ ...emptyValues })
  const [errors, setErrors] = useState({ ...emptyValues })
  const [touched, setTouched] = useState({ ...emptyTouched })
  const [isSubmitting, setIsSubmitting] = useState(false)
  const [isSuccess, setIsSuccess] = useState(false)
  const timers = useRef([])
  const locked = useRef(false)
  useEffect(() => () => timers.current.forEach(clearTimeout), [])
  function reset() {
    timers.current.forEach(clearTimeout)
    timers.current = []
    locked.current = false
    setValues({ ...emptyValues })
    setErrors({ ...emptyValues })
    setTouched({ ...emptyTouched })
    setIsSubmitting(false)
    setIsSuccess(false)
  }
  function handleChange(event) {
    const { name, value } = event.target
    const next = { ...values, [name]: value }
    setValues(next)
    if (touched[name]) setErrors((previous) => ({ ...previous, [name]: validateContactForm(next)[name] }))
  }
  function handleBlur(event) {
    const { name } = event.target
    setTouched((previous) => ({ ...previous, [name]: true }))
    setErrors((previous) => ({ ...previous, [name]: validateContactForm(values)[name] }))
  }
  function handleSubmit(event) {
    event.preventDefault()
    if (locked.current) return
    const validation = validateContactForm(values)
    setErrors(validation)
    setTouched({ name: true, email: true, subject: true, message: true })
    if (Object.values(validation).some(Boolean)) return
    locked.current = true
    setIsSubmitting(true)
    timers.current.push(setTimeout(() => {
      setIsSubmitting(false)
      setIsSuccess(true)
      timers.current.push(setTimeout(reset, 4000))
    }, 1000))
  }
  return { values, errors, touched, isSubmitting, isSuccess, handleChange, handleBlur, handleSubmit, reset }
}
```

### FILE: src/components/ui/Button.jsx
```jsx
const variants = {
  primary: 'bg-indigo-500 text-white hover:bg-indigo-400',
  secondary: 'border border-slate-700 bg-slate-900 text-slate-200 hover:border-indigo-400',
  ghost: 'text-slate-300 hover:bg-slate-800 hover:text-white',
}
const sizes = { sm: 'px-3 py-2 text-sm', md: 'px-5 py-3 text-sm', lg: 'px-6 py-3.5 text-base' }
export default function Button({ children, variant = 'primary', size = 'md', disabled = false, type = 'button', onClick, className = '' }) {
  return <button type={type} disabled={disabled} onClick={onClick} className={`inline-flex items-center justify-center gap-2 rounded-lg font-medium transition-all enabled:hover:-translate-y-0.5 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-indigo-400 focus-visible:ring-offset-2 focus-visible:ring-offset-slate-900 disabled:cursor-not-allowed disabled:opacity-50 ${variants[variant]} ${sizes[size]} ${className}`}>{children}</button>
}
```

### FILE: src/components/ui/Input.jsx
```jsx
export default function Input({ label, name, type = 'text', value, onChange, onBlur, error = '', placeholder, disabled = false }) {
  return (
    <div>
      <label htmlFor={name} className="mb-2 block text-sm font-medium text-slate-200">{label}</label>
      <input id={name} name={name} type={type} value={value} onChange={onChange} onBlur={onBlur} placeholder={placeholder} disabled={disabled} required aria-invalid={Boolean(error)} aria-describedby={error ? `${name}-error` : undefined} className={`w-full rounded-lg border bg-slate-950/50 px-4 py-3 text-slate-200 placeholder:text-slate-500 focus:outline-none focus:ring-2 focus:ring-indigo-400 disabled:opacity-60 ${error ? 'border-red-400' : 'border-slate-700'}`} />
      {error && <p id={`${name}-error`} className="mt-2 text-sm text-red-400">{error}</p>}
    </div>
  )
}
```

### FILE: src/components/ui/Textarea.jsx
```jsx
export default function Textarea({ label, name, value, onChange, onBlur, error = '', placeholder, disabled = false }) {
  return (
    <div>
      <label htmlFor={name} className="mb-2 block text-sm font-medium text-slate-200">{label}</label>
      <textarea id={name} name={name} rows={5} value={value} onChange={onChange} onBlur={onBlur} placeholder={placeholder} disabled={disabled} required aria-invalid={Boolean(error)} aria-describedby={error ? `${name}-error` : undefined} className={`w-full rounded-lg border bg-slate-950/50 px-4 py-3 text-slate-200 placeholder:text-slate-500 focus:outline-none focus:ring-2 focus:ring-indigo-400 disabled:opacity-60 resize-y ${error ? 'border-red-400' : 'border-slate-700'}`} />
      {error && <p id={`${name}-error`} className="mt-2 text-sm text-red-400">{error}</p>}
    </div>
  )
}
```

### FILE: src/components/ui/SectionTitle.jsx
```jsx
export default function SectionTitle({ title, subtitle, align = 'left' }) {
  return <div className={`mb-10 ${align === 'center' ? 'text-center' : 'text-left'}`}><h2 className="text-3xl font-bold tracking-tight text-white sm:text-4xl">{title}</h2>{subtitle && <p className="mt-3 text-slate-400">{subtitle}</p>}</div>
}
```

### FILE: src/components/portfolio/SkillBadge.jsx
```jsx
export default function SkillBadge({ skill }) {
  return <span className="inline-flex items-center rounded-full border border-slate-700 bg-slate-800/50 px-4 py-2 text-sm text-slate-300 transition-all hover:border-indigo-400 hover:text-indigo-300">{skill}</span>
}
```

### FILE: src/components/portfolio/ProjectCard.jsx
```jsx
import { ArrowUpRight, Code2, FileCode2 as Github } from 'lucide-react'
const gradients = ['from-indigo-600/40 to-purple-500/10', 'from-cyan-600/40 to-blue-500/10', 'from-purple-600/40 to-pink-500/10']
function ProjectLink({ href, children }) {
  if (!href || href === '#') return <span aria-disabled="true" title="Link coming soon" className="inline-flex items-center gap-2 text-sm text-slate-500">{children}<span className="sr-only"> (coming soon)</span></span>
  return <a href={href} target="_blank" rel="noopener noreferrer" className="inline-flex items-center gap-2 text-sm text-slate-300 transition-colors hover:text-indigo-300">{children}</a>
}
export default function ProjectCard({ project }) {
  return (
    <article className="flex h-full min-w-0 flex-col overflow-hidden rounded-xl border border-slate-800 bg-slate-900 transition-all hover:-translate-y-1 hover:border-slate-600">
      <div aria-hidden="true" className={`flex h-40 items-center justify-between bg-gradient-to-br ${gradients[(project.id - 1) % gradients.length]} p-8`}><Code2 size={48} className="text-white/70" /><span className="text-5xl font-bold text-white/10">{String(project.id).padStart(2, '0')}</span></div>
      <div className="flex flex-1 flex-col p-6"><h3 className="text-xl font-semibold text-white">{project.title}</h3><p className="mt-3 text-sm leading-7 text-slate-400">{project.description}</p><div className="my-5 flex flex-wrap gap-2">{project.tech.map((tech) => <span key={tech} className="rounded-full bg-indigo-500/10 px-3 py-1 text-xs text-indigo-300">{tech}</span>)}</div><div className="mt-auto flex flex-wrap gap-5 border-t border-slate-800 pt-5"><ProjectLink href={project.link}>Live Demo <ArrowUpRight size={16} /></ProjectLink><ProjectLink href={project.repo}>Code <Github size={16} /></ProjectLink></div></div>
    </article>
  )
}
```

### FILE: src/pages/Home.jsx
```jsx
import { Link } from 'react-router-dom'
import { ArrowRight, Code2 } from 'lucide-react'
import SectionTitle from '../components/ui/SectionTitle'
import SkillBadge from '../components/portfolio/SkillBadge'
const skills = ['React', 'JavaScript', 'HTML', 'CSS', 'Tailwind CSS', 'Git', 'GitHub', 'Vite', 'REST APIs', 'Responsive Design', 'UI/UX Basics', 'Problem Solving']
const linkStyle = 'inline-flex items-center justify-center gap-2 rounded-lg px-6 py-3 font-medium transition-all hover:-translate-y-0.5 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-indigo-400'
export default function Home() {
  return (
    <div className="mx-auto max-w-6xl px-4 sm:px-6 lg:px-8">
      <section className="grid items-center gap-12 py-20 lg:grid-cols-[1.4fr_1fr] lg:py-28">
        <div><p className="mb-4 text-lg text-indigo-300">Hi, I'm</p><h1 className="text-5xl font-bold leading-tight tracking-tight text-white sm:text-6xl lg:text-7xl">Bassem Djeddi<span className="text-indigo-400">.</span></h1><p className="mt-5 text-2xl text-slate-300">Developer</p><p className="mt-6 max-w-xl leading-8 text-slate-400">I'm Bassem Djeddi, a passionate developer focused on building clean, modern web interfaces. I enjoy turning ideas into functional, user-friendly applications and I'm constantly learning new technologies to sharpen my craft.</p><div className="mt-8 flex flex-wrap gap-4"><Link to="/projects" className={`${linkStyle} bg-indigo-500 text-white hover:bg-indigo-400`}>View Projects <ArrowRight size={18} /></Link><Link to="/contact" className={`${linkStyle} border border-slate-700 hover:border-indigo-400`}>Get in Touch</Link></div></div>
        <div aria-hidden="true" className="relative overflow-hidden rounded-2xl border border-slate-800 bg-gradient-to-br from-indigo-500/20 via-slate-900 to-slate-900 p-8 sm:p-10"><Code2 size={40} className="mb-12 text-indigo-300" /><p className="font-mono text-sm text-slate-400">const developer = {'{'}</p><p className="my-5 pl-4 font-mono text-sm leading-8 text-indigo-200">name: 'Bassem Djeddi',<br />focus: 'Web interfaces',<br />mindset: 'Always learning'</p><p className="font-mono text-sm text-slate-400">{'}'}</p><div className="mt-12 border-t border-slate-700 pt-5 text-sm text-slate-400">Thoughtful interfaces. Practical solutions.</div></div>
      </section>
      <section className="border-t border-slate-800 py-20"><SectionTitle title="Skills & Technologies" subtitle="The tools I use to bring ideas to life." /><div className="flex flex-wrap gap-3">{skills.map((skill) => <SkillBadge key={skill} skill={skill} />)}</div></section>
      <section className="grid gap-8 border-t border-slate-800 py-20 md:grid-cols-[1fr_2fr]"><SectionTitle title="About Me" subtitle="Curiosity meets code." /><div className="space-y-5 leading-8 text-slate-400"><p>I enjoy the process of turning an idea into something people can use. My focus is on clear layouts, thoughtful interactions, and interfaces that feel comfortable on any screen.</p><p>I build with JavaScript and React, breaking interfaces into reusable components and paying attention to the details that make them work well together.</p><p>Learning is part of my everyday approach. Each project is an opportunity to explore a new tool, improve my problem-solving skills, and make the next experience better.</p></div></section>
      <section className="mb-20 rounded-xl border border-indigo-500/20 bg-indigo-500/10 p-8 sm:p-12"><h2 className="text-3xl font-bold text-white">Let's work together</h2><p className="mb-6 mt-3 text-slate-400">Have an idea or want to connect? I'd love to hear from you.</p><Link to="/contact" className={`${linkStyle} bg-indigo-500 text-white hover:bg-indigo-400`}>Get in Touch <ArrowRight size={18} /></Link></section>
    </div>
  )
}
```

### FILE: src/pages/Projects.jsx
```jsx
import { projects } from '../data/projects'
import ProjectCard from '../components/portfolio/ProjectCard'
import SectionTitle from '../components/ui/SectionTitle'
export default function Projects() {
  return <section className="mx-auto max-w-6xl px-4 py-20 sm:px-6 lg:px-8"><SectionTitle title="Projects" subtitle="A selection of things I've built" /><p className="mb-8 text-sm text-slate-400">Demo and code links will be added as projects become available.</p>{projects.length ? <div className="grid grid-cols-1 gap-6 md:grid-cols-2 lg:grid-cols-3">{projects.map((project) => <ProjectCard key={project.id} project={project} />)}</div> : <p className="rounded-xl border border-slate-800 p-8 text-slate-400">New projects are on the way. Check back soon.</p>}</section>
}
```

### FILE: src/pages/Contact.jsx
```jsx
import { Mail, ContactRound as Linkedin, ArrowUpRight } from 'lucide-react'
import SectionTitle from '../components/ui/SectionTitle'
import Input from '../components/ui/Input'
import Textarea from '../components/ui/Textarea'
import Button from '../components/ui/Button'
import useContactForm from '../hooks/useContactForm'
import { validateContactForm } from '../utils/validators'
export default function Contact() {
  const { values, errors, touched, isSubmitting, isSuccess, handleChange, handleBlur, handleSubmit } = useContactForm()
  const invalid = Object.values(validateContactForm(values)).some(Boolean)
  const fieldProps = (name) => ({ name, value: values[name], onChange: handleChange, onBlur: handleBlur, error: touched[name] ? errors[name] : '', disabled: isSubmitting || isSuccess })
  return (
    <section className="mx-auto max-w-6xl px-4 py-20 sm:px-6 lg:px-8"><SectionTitle title="Contact" subtitle="Let's get in touch" />
      <div className="grid items-start gap-8 md:grid-cols-[1fr_1.5fr]">
        <aside className="min-w-0 rounded-xl border border-slate-800 bg-slate-900 p-6 sm:p-8"><h2 className="text-xl font-semibold text-white">Start a conversation</h2><p className="mb-8 mt-3 leading-7 text-slate-400">For ideas, opportunities, or a simple hello, reach out directly using the links below.</p><div className="space-y-7"><div><p className="mb-2 flex items-center gap-2 text-sm text-slate-400"><Mail size={18} /> Email</p><a className="break-all text-indigo-300 hover:text-indigo-200" href="mailto:Bassemdjeddi78@gmail.com">Bassemdjeddi78@gmail.com</a></div><div><p className="mb-2 flex items-center gap-2 text-sm text-slate-400"><Linkedin size={18} /> LinkedIn</p><a href="https://www.linkedin.com/in/bassem-djeddi-835682360/" target="_blank" rel="noopener noreferrer" className="inline-flex items-center gap-2 text-indigo-300 hover:text-indigo-200">Bassem Djeddi <ArrowUpRight size={16} /></a></div></div></aside>
        <form noValidate onSubmit={handleSubmit} className="min-w-0 space-y-5 rounded-xl border border-slate-800 bg-slate-900 p-6 sm:p-8"><h2 className="text-xl font-semibold text-white">Send a message</h2><p className="text-sm text-slate-400">Course demo: this form simulates submission and does not deliver email. All fields are required.</p><Input label="Name" placeholder="Your name" {...fieldProps('name')} /><Input label="Email" type="email" placeholder="you@example.com" {...fieldProps('email')} /><Input label="Subject" placeholder="What would you like to discuss?" {...fieldProps('subject')} /><Textarea label="Message" placeholder="Tell me about your idea…" {...fieldProps('message')} /><Button type="submit" disabled={invalid || isSubmitting || isSuccess} className="w-full sm:w-auto">{isSubmitting ? 'Sending...' : isSuccess ? '✓ Sent!' : 'Send Message'}</Button><div role="status" aria-live="polite">{isSuccess && <p className="rounded-lg border border-emerald-500/30 bg-emerald-500/10 p-4 text-sm text-emerald-300">Demo submission successful! No email was sent. To reach me, use the email link. The form will reset shortly.</p>}</div></form>
      </div>
    </section>
  )
}
```

### FILE: README.md
```markdown
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
```

### FILE: tailwind.config.js
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./index.html', './src/**/*.{js,jsx}'],
  theme: {
    extend: {
      colors: { dark: '#0f172a', primary: '#6366f1' },
      fontFamily: { sans: ['Inter', 'system-ui', 'sans-serif'] },
    },
  },
  plugins: [],
}

```


### FILE: src/components/layout/Footer.jsx
```jsx
import { FileCode2 as Github, ContactRound as Linkedin, Mail } from 'lucide-react'

export default function Footer() {
  const year = new Date().getFullYear()

  return (
    <footer className="border-t border-slate-800 mt-20">
      <div className="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="flex flex-col sm:flex-row items-center justify-between gap-4">
          <p className="text-sm text-slate-400">
            © {year} Bassem Djeddi. All rights reserved.
          </p>

          <div className="flex items-center gap-4">
            <a
              href="https://github.com"
              target="_blank"
              rel="noopener noreferrer"
              aria-label="GitHub"
              className="text-slate-400 hover:text-primary transition-colors"
            >
              <Github size={20} />
            </a>
            <a
              href="https://www.linkedin.com/in/bassem-djeddi-835682360/"
              target="_blank"
              rel="noopener noreferrer"
              aria-label="LinkedIn"
              className="text-slate-400 hover:text-primary transition-colors"
            >
              <Linkedin size={20} />
            </a>
            <a
              href="mailto:Bassemdjeddi78@gmail.com"
              aria-label="Email"
              className="text-slate-400 hover:text-primary transition-colors"
            >
              <Mail size={20} />
            </a>
          </div>
        </div>
      </div>
    </footer>
  )
}
```


### FILE: src/main.jsx
```jsx
import { StrictMode } from 'react'
import { BrowserRouter } from 'react-router-dom'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.jsx'

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>,
)
```
