{
  "name": "lycee-saint-jean-site",
  "private": true,
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
/** @type {import('next').NextConfig} */
const nextConfig = {}

module.exports = nextConfig
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
    "./app/**/*.{js,ts,jsx,tsx}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
@tailwind base;
@tailwind components;
@tailwind utilities;
import '@/styles/globals.css'
export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}
export default function Home() {
  return (
    <main className="flex min-h-screen flex-col items-center justify-center bg-gray-100">
      <h1 className="text-4xl font-bold text-blue-600 mb-4">
        Lycée Saint Jean - Bienvenue !
      </h1>
      <p className="text-lg text-gray-700">
        Ceci est la page d’accueil de votre projet Next.js + Tailwind CSS.
      </p>
    </main>
  )
}
