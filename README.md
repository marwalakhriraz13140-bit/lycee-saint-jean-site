# Create a full Next.js + Tailwind multi-page project and zip it for download
import os, json, textwrap, zipfile, pathlib

project_root = "/mnt/data/lycee-saint-jean-site"
os.makedirs(project_root, exist_ok=True)

# Basic files
package_json = {
  "name": "lycee-saint-jean-site",
  "private": True,
  "version": "1.0.0",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "14.2.5",
    "react": "18.3.1",
    "react-dom": "18.3.1"
  },
  "devDependencies": {
    "autoprefixer": "10.4.20",
    "postcss": "8.4.41",
    "tailwindcss": "3.4.10",
    "typescript": "5.5.4",
    "@types/react": "18.3.3",
    "@types/node": "20.11.30"
  }
}
with open(os.path.join(project_root, "package.json"), "w", encoding="utf-8") as f:
    json.dump(package_json, f, indent=2)

# tsconfig
tsconfig = {
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": False,
    "skipLibCheck": True,
    "strict": True,
    "forceConsistentCasingInFileNames": True,
    "noEmit": True,
    "esModuleInterop": True,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": True,
    "isolatedModules": True,
    "jsx": "preserve",
    "incremental": True,
    "baseUrl": ".",
    "paths": { "@/*": ["./*"] }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
with open(os.path.join(project_root, "tsconfig.json"), "w", encoding="utf-8") as f:
    json.dump(tsconfig, f, indent=2)

# next-env.d.ts
open(os.path.join(project_root, "next-env.d.ts"), "w").write("/// <reference types=\"next\" />\n/// <reference types=\"next/image-types/global\" />\n")

# next.config
open(os.path.join(project_root, "next.config.mjs"), "w").write("export default { reactStrictMode: true };\n")

# PostCSS & Tailwind
open(os.path.join(project_root, "postcss.config.js"), "w").write('module.exports = { plugins: { tailwindcss: {}, autoprefixer: {}, }, };\n')
open(os.path.join(project_root, "tailwind.config.ts"), "w").write(textwrap.dedent('''
  import type { Config } from "tailwindcss";
  const config: Config = {
    content: ["./app/**/*.{ts,tsx}", "./components/**/*.{ts,tsx}"],
    theme: { extend: {} },
    plugins: [],
  };
  export default config;
'''))

# app directory and globals
os.makedirs(os.path.join(project_root, "app"), exist_ok=True)
open(os.path.join(project_root, "app", "globals.css"), "w").write(textwrap.dedent('''
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  
  :root { --ring: 0 0% 0%; }
  body { @apply bg-white text-zinc-900 antialiased; }
  .container { @apply max-w-6xl mx-auto px-4; }
  .btn { @apply inline-flex items-center gap-2 rounded-2xl px-4 py-2 font-medium shadow hover:shadow-md transition; }
  .btn-primary { @apply btn bg-zinc-900 text-white; }
  .btn-outline { @apply btn border border-zinc-300 bg-white; }
  .card { @apply rounded-2xl border border-zinc-200 shadow-sm p-6; }
  header.sticky { @apply sticky top-0 z-50 bg-white/80 backdrop-blur supports-[backdrop-filter]:bg-white/60 border-b; }
  nav a { @apply text-sm font-medium hover:underline; }
  footer { @apply border-t mt-16; }
'''))

# Layout
layout_tsx = textwrap.dedent('''
  import type { Metadata } from "next";
  import "./globals.css";
  import Link from "next/link";
  
  export const metadata: Metadata = {
    title: "Lycée Saint-Jean — Pôle Sup",
    description: "Maquette de site multi-pages pour BTS du Lycée Saint-Jean (Salon-de-Provence)",
  };
  
  export default function RootLayout({ children }: { children: React.ReactNode }) {
    return (
      <html lang="fr">
        <body>
          <header className="sticky">
            <div className="container flex items-center justify-between py-3">
              <Link href="/" className="flex items-center gap-3">
                {/* Logo externe vers le site officiel */}
                <img
                  src="https://www.lyceesaintjean.com/IMG/siteon0.svg"
                  alt="Lycée Saint-Jean logo"
                  className="h-10 w-auto"
                />
                <span className="text-base font-semibold">Pôle Sup Saint-Jean</span>
              </Link>
              <nav className="flex gap-6">
                <Link href="/formations">Formations</Link>
                <Link href="/resultats">Résultats</Link>
                <Link href="/contact">Contact</Link>
              </nav>
            </div>
          </header>
          <main className="container">{children}</main>
          <footer className="">
            <div className="container py-8 text-sm text-zinc-600">
              <div className="flex flex-wrap items-center gap-3">
                <span>© {new Date().getFullYear()} OGEC Saint-Jean</span>
                <a href="https://www.lyceesaintjean.com/" target="_blank" className="hover:underline">Site officiel</a>
                <span className="mx-2">•</span>
                <a href="https://www.google.com/maps?q=76+avenue+Georges+Borel+13300+Salon-de-Provence" target="_blank" className="hover:underline">
                  76 avenue Georges Borel — 13300 Salon-de-Provence
                </a>
                <span className="mx-2">•</span>
                <a href="mailto:accueil@polesupstjean.com" className="hover:underline">accueil@polesupstjean.com</a>
                <span className="mx-2">•</span>
                <a href="tel:+33490532051" className="hover:underline">04 90 53 20 51</a>
              </div>
            </div>
          </footer>
        </body>
      </html>
    );
  }
''')
open(os.path.join(project_root, "app", "layout.tsx"), "w").write(layout_tsx)

# Home page
home_tsx = textwrap.dedent('''
  import Link from "next/link";
  
  export default function Page() {
    return (
      <section className="py-10">
        <div className="grid gap-10">
          <div className="card bg-gradient-to-br from-zinc-50 to-white">
            <h1 className="text-3xl md:text-4xl font-bold">Pôle Sup Saint‑Jean — BTS</h1>
            <p className="mt-3 text-lg text-zinc-600">
              Maquette de site pour valoriser les BTS du Lycée Saint‑Jean (Salon‑de‑Provence) avec des liens réels vers le site officiel.
            </p>
            <div className="mt-6 flex gap-3">
              <Link className="btn-primary" href="/formations">Voir les formations</Link>
              <Link className="btn-outline" href="/resultats">Résultats 2025</Link>
            </div>
          </div>
          <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
            {[
              { slug: "bts-mco", title: "BTS MCO", desc: "Management Commercial Opérationnel" },
              { slug: "bts-cg", title: "BTS CG", desc: "Comptabilité et Gestion" },
              { slug: "bts-cjn", title: "BTS CJN", desc: "Collaborateur Juriste Notarial" },
              { slug: "bts-pi", title: "BTS PI", desc: "Professions Immobilières" },
              { slug: "bts-ndrc", title: "BTS NDRC", desc: "Négociation et Digitalisation de la Relation Client" },
            ].map(card => (
              <Link key={card.slug} href={`/formations/${card.slug}`} className="card hover:shadow-md transition">
                <h3 className="text-xl font-semibold">{card.title}</h3>
                <p className="text-sm text-zinc-600">{card.desc}</p>
                <span className="mt-4 inline-block text-sm underline">Découvrir la formation</span>
              </Link>
            ))}
          </div>
        </div>
      </section>
    );
  }
''')
open(os.path.join(project_root, "app", "page.tsx"), "w").write(home_tsx)

# Formations index
os.makedirs(os.path.join(project_root, "app", "formations"), exist_ok=True)
formations_index = textwrap.dedent('''
  import Link from "next/link";
  
  const formations = [
    { slug: "bts-mco", title: "BTS MCO", sousTitre: "Management Commercial Opérationnel",
      lienOfficiel: "https://www.lyceesaintjean.com/BTS-MCO-INITIAL.html",
      alternance: "https://www.lyceesaintjean.com/BTS-MCO-EN-ALTERNANCE.html" },
    { slug: "bts-cg", title: "BTS CG", sousTitre: "Comptabilité et Gestion",
      lienOfficiel: "https://www.lyceesaintjean.com/BTS-CG-INITIAL.html",
      alternance: "https://www.lyceesaintjean.com/BTS-CG-EN-ALTERNANCE.html" },
    { slug: "bts-cjn", title: "BTS CJN", sousTitre: "Collaborateur Juriste Notarial",
      lienOfficiel: "https://www.lyceesaintjean.com/BTS-CJN-INITIAL.html" },
    { slug: "bts-pi", title: "BTS PI", sousTitre: "Professions Immobilières (initial hors contrat)",
      lienOfficiel: "https://www.lyceesaintjean.com/BTS-PI-INITIAL-HORS-CONTRAT.html",
      alternance: "https://www.lyceesaintjean.com/BTS-PI-EN-ALTERNANCE.html" },
    { slug: "bts-ndrc", title: "BTS NDRC", sousTitre: "Négociation & Digitalisation de la Relation Client",
      alternance: "https://www.lyceesaintjean.com/BTS-NDRC-ALTERNANCE.html" },
  ];
  
  export default function Page() {
    return (
      <section className="py-10">
        <h1 className="text-3xl font-bold">Formations</h1>
        <p className="text-zinc-600 mt-2">Choisissez un BTS pour voir la page de présentation.</p>
        <div className="grid md:grid-cols-2 gap-6 mt-6">
          {formations.map(f => (
            <div key={f.slug} className="card">
              <h3 className="text-xl font-semibold">{f.title}</h3>
              <p className="text-sm text-zinc-600">{f.sousTitre}</p>
              <div className="mt-4 flex flex-wrap gap-3">
                <Link className="btn-primary" href={`/formations/${f.slug}`}>Page maquette</Link>
                {f.lienOfficiel && <a className="btn-outline" href={f.lienOfficiel} target="_blank">Page officielle</a>}
                {f.alternance && <a className="btn-outline" href={f.alternance} target="_blank">Alternance</a>}
              </div>
            </div>
          ))}
        </div>
      </section>
    );
  }
''')
open(os.path.join(project_root, "app", "formations", "page.tsx"), "w").write(formations_index)

# Template for BTS pages
bts_pages = {
  "bts-mco": {
    "title": "BTS MCO",
    "subtitle": "Management Commercial Opérationnel",
    "officiel": "https://www.lyceesaintjean.com/BTS-MCO-INITIAL.html",
    "alternance": "https://www.lyceesaintjean.com/BTS-MCO-EN-ALTERNANCE.html"
  },
  "bts-cg": {
    "title": "BTS CG",
    "subtitle": "Comptabilité et Gestion",
    "officiel": "https://www.lyceesaintjean.com/BTS-CG-INITIAL.html",
    "alternance": "https://www.lyceesaintjean.com/BTS-CG-EN-ALTERNANCE.html"
  },
  "bts-cjn": {
    "title": "BTS CJN",
    "subtitle": "Collaborateur Juriste Notarial",
    "officiel": "https://www.lyceesaintjean.com/BTS-CJN-INITIAL.html",
    "alternance": None
  },
  "bts-pi": {
    "title": "BTS PI",
    "subtitle": "Professions Immobilières",
    "officiel": "https://www.lyceesaintjean.com/BTS-PI-INITIAL-HORS-CONTRAT.html",
    "alternance": "https://www.lyceesaintjean.com/BTS-PI-EN-ALTERNANCE.html"
  },
  "bts-ndrc": {
    "title": "BTS NDRC",
    "subtitle": "Négociation & Digitalisation de la Relation Client",
    "officiel": None,
    "alternance": "https://www.lyceesaintjean.com/BTS-NDRC-ALTERNANCE.html"
  }
}

os.makedirs(os.path.join(project_root, "app", "formations"), exist_ok=True)

bts_template = '''
  import Link from "next/link";
  
  export default function Page() {{
    return (
      <section className="py-10">
        <div className="flex items-start justify-between gap-6 flex-wrap">
          <div>
            <h1 className="text-3xl font-bold">{title}</h1>
            <p className="text-zinc-600 mt-1">{subtitle}</p>
          </div>
          <div className="flex gap-3">
            <Link className="btn-outline" href="/formations">← Retour</Link>
            {links}
          </div>
        </div>
        <div className="grid md:grid-cols-3 gap-6 mt-6">
          <div className="md:col-span-2 card">
            <h2 className="text-xl font-semibold">Pourquoi choisir ce BTS au Lycée Saint‑Jean ?</h2>
            <ul className="list-disc pl-6 mt-3 space-y-1 text-zinc-700">
              <li>Encadrement de proximité et accompagnement individualisé.</li>
              <li>Stages en entreprise et modules professionnalisants.</li>
              <li>Rythme et modalités conformes à la page officielle.</li>
            </ul>
          </div>
          <aside className="card">
            <h3 className="font-semibold">Infos pratiques</h3>
            <ul className="mt-2 text-sm space-y-1 text-zinc-700">
              <li><a className="underline" href="https://www.parcoursup.fr" target="_blank">Parcoursup</a> (si formation initiale)</li>
              <li><a className="underline" href="mailto:accueil@polesupstjean.com">accueil@polesupstjean.com</a></li>
              <li><a className="underline" href="tel:+33490532051">04 90 53 20 51</a></li>
              <li><a className="underline" href="https://www.google.com/maps?q=76+avenue+Georges+Borel+13300+Salon-de-Provence" target="_blank">Adresse & plan</a></li>
            </ul>
          </aside>
        </div>
      </section>
    );
  }}
'''
for slug, cfg in bts_pages.items():
    folder = os.path.join(project_root, "app", "formations", slug)
    os.makedirs(folder, exist_ok=True)
    links_fragments = []
    if cfg["officiel"]:
        links_fragments.append(f'<a className="btn-primary" href="{cfg["officiel"]}" target="_blank">Page officielle</a>')
    if cfg["alternance"]:
        links_fragments.append(f'<a className="btn-outline" href="{cfg["alternance"]}" target="_blank">Alternance</a>')
    links_jsx = " ".join(links_fragments) if links_fragments else "<span />"
    content = bts_template.replace("{title}", cfg["title"]).replace("{subtitle}", cfg["subtitle"]).replace("{links}", links_jsx)
    open(os.path.join(folder, "page.tsx"), "w").write(content)

# Résultats page (données 2025 issues du site)
os.makedirs(os.path.join(project_root, "app", "resultats"), exist_ok=True)
resultats_tsx = textwrap.dedent('''
  export default function Page() {
    const lignes = [
      { filiere: "Comptabilité Gestion (Lycée)", candidats: 7, admis: 5, taux: "71%" },
      { filiere: "MCO (Lycée)", candidats: 19, admis: 18, taux: "95%" },
      { filiere: "CJN (Lycée)", candidats: 15, admis: 10, taux: "73%" },
      { filiere: "PI (CFA)", candidats: 13, admis: 12, taux: "92%" },
      { filiere: "MCO (CFA)", candidats: 10, admis: 8, taux: "80%" },
      { filiere: "NDRC (CFA)", candidats: 6, admis: 6, taux: "100%" },
      { filiere: "CG (CFA)", candidats: 4, admis: 4, taux: "100%" },
    ];
    return (
      <section className="py-10">
        <h1 className="text-3xl font-bold">Résultats 2025</h1>
        <p className="text-zinc-600 mt-2">Source : page d’accueil du site officiel.</p>
        <div className="mt-6 overflow-x-auto">
          <table className="min-w-full text-sm">
            <thead>
              <tr className="text-left">
                <th className="p-3">Filière</th>
                <th className="p-3">Candidats</th>
                <th className="p-3">Reçus</th>
                <th className="p-3">Taux</th>
              </tr>
            </thead>
            <tbody>
              {lignes.map((l, i) => (
                <tr key={i} className="odd:bg-zinc-50">
                  <td className="p-3">{l.filiere}</td>
                  <td className="p-3">{l.candidats}</td>
                  <td className="p-3">{l.admis}</td>
                  <td className="p-3">{l.taux}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </section>
    );
  }
''')
open(os.path.join(project_root, "app", "resultats", "page.tsx"), "w").write(resultats_tsx)

# Contact page
os.makedirs(os.path.join(project_root, "app", "contact"), exist_ok=True)
contact_tsx = textwrap.dedent('''
  export default function Page() {
    return (
      <section className="py-10 grid gap-6">
        <h1 className="text-3xl font-bold">Contact</h1>
        <div className="grid md:grid-cols-2 gap-6">
          <div className="card">
            <h2 className="text-xl font-semibold">Coordonnées</h2>
            <ul className="mt-3 space-y-2 text-sm">
              <li><strong>Téléphone :</strong> <a className="underline" href="tel:+33490532051">04 90 53 20 51</a></li>
              <li><strong>E-mail :</strong> <a className="underline" href="mailto:accueil@polesupstjean.com">accueil@polesupstjean.com</a></li>
              <li><strong>Adresse :</strong> 76 avenue Georges Borel, 13300 Salon‑de‑Provence</li>
              <li><a className="underline" href="https://www.lyceesaintjean.com/" target="_blank">Site officiel</a></li>
              <li><a className="underline" href="https://www.facebook.com/p/Lyc%C3%A9e-saint-jean-Salon-de-Provence-100056737670407/" target="_blank">Facebook</a></li>
            </ul>
          </div>
          <div className="card">
            <h2 className="text-xl font-semibold">Plan d'accès</h2>
            <iframe
              className="rounded-2xl w-full h-64"
              loading="lazy"
              referrerPolicy="no-referrer-when-downgrade"
              src="https://www.google.com/maps?q=76+avenue+Georges+Borel+13300+Salon-de-Provence&output=embed">
            </iframe>
            <a className="btn-outline mt-3 inline-block" target="_blank" href="https://fr.mappy.com/plan/76-avenue-georges-borel-13300-salon-de-provence">Ouvrir dans Mappy</a>
          </div>
        </div>
      </section>
    );
  }
''')
open(os.path.join(project_root, "app", "contact", "page.tsx"), "w").write(contact_tsx)

# Basic page redirect for /formations/* already created

# Create README with instructions
readme = textwrap.dedent('''
  # Maquette — Lycée Saint‑Jean (Salon‑de‑Provence)
  
  Projet Next.js + Tailwind, multi‑pages (Formations et pages BTS) avec liens réels vers le site officiel.
  
  ## Lancer en local
  ```bash
  npm install
  npm run dev
  # http://localhost:3000

