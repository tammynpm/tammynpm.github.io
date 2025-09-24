# Tammy — Personal Website

A modern, single-page personal site tailored for a low-level/DevOps/Security profile. Built with Tailwind (CDN), no build step required.

## Features
- Dark/Light theme with persistence
- Responsive nav + mobile menu
- Sections: Hero, About, Skills, Experience, Projects, Writing, Contact
- Clean typography (Inter + JetBrains Mono)

## Getting Started
1. Serve the folder or open `index.html` directly.
   - Quick local server (Python):
     ```bash
     python3 -m http.server 8000
     ```
   - Then open http://localhost:8000
2. Edit content in `index.html`. Update email/social links in the Contact section.
3. Customize colors and spacing via Tailwind classes or `assets/css/styles.css`.

## Customize
- Branding: Search for `brand` in `index.html` (Tailwind extended color) or update Tailwind config in the `<script>` tag.
- Sections: Duplicate project cards or experience items as needed.
- Fonts: Adjust Google Fonts in `<head>` if you prefer different fonts.

## Deploy
- Any static host works (GitHub Pages, Netlify, Vercel, S3).

## License
MIT
