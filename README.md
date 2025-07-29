# 🎨 Laravel Layout Best Practice - Tailwind CSS v4

## 📁 Struktur Layout yang Direkomendasikan

```
resources/views/
├── layouts/
│   ├── app.blade.php           # Main layout
│   ├── guest.blade.php         # Layout untuk guest (login, register)
│   └── components/
│       ├── navigation.blade.php
│       ├── footer.blade.php
│       └── meta.blade.php
├── components/
│   ├── button.blade.php
│   ├── card.blade.php
│   └── alert.blade.php
└── pages/
    ├── home.blade.php
    ├── about.blade.php
    └── contact.blade.php
```

---

## 🏗️ 1. Buat Layout Utama (`app.blade.php`)

### `resources/views/layouts/app.blade.php`:
```blade
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}" class="scroll-smooth">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="csrf-token" content="{{ csrf_token() }}">

    <!-- SEO Meta Tags -->
    <title>@yield('title', config('app.name', 'Laravel'))</title>
    <meta name="description" content="@yield('description', 'Default description')">
    <meta name="keywords" content="@yield('keywords', 'laravel, tailwind, php')">
    
    <!-- Open Graph / Facebook -->
    <meta property="og:type" content="website">
    <meta property="og:url" content="{{ url()->current() }}">
    <meta property="og:title" content="@yield('title', config('app.name'))">
    <meta property="og:description" content="@yield('description', 'Default description')">
    
    <!-- Twitter -->
    <meta property="twitter:card" content="summary_large_image">
    <meta property="twitter:url" content="{{ url()->current() }}">
    <meta property="twitter:title" content="@yield('title', config('app.name'))">
    <meta property="twitter:description" content="@yield('description', 'Default description')">

    <!-- Favicon -->
    <link rel="icon" type="image/x-icon" href="{{ asset('favicon.ico') }}">
    
    <!-- Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    
    <!-- Vite Assets -->
    @vite(['resources/css/app.css', 'resources/js/app.js'])
    
    <!-- Custom Styles -->
    @stack('styles')
</head>
<body class="bg-gray-50 dark:bg-gray-900 text-gray-900 dark:text-gray-100 font-sans antialiased">
    <!-- Loading Spinner -->
    <div id="loading" class="fixed inset-0 bg-white dark:bg-gray-900 z-50 flex items-center justify-center">
        <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-indigo-600"></div>
    </div>

    <!-- Skip Navigation -->
    <a href="#main-content" class="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 bg-indigo-600 text-white px-4 py-2 rounded-md z-50">
        Skip to main content
    </a>

    <!-- Navigation -->
    @include('layouts.components.navigation')

    <!-- Main Content -->
    <main id="main-content" class="min-h-screen">
        <!-- Page Header (Optional) -->
        @hasSection('header')
            <header class="bg-white dark:bg-gray-800 shadow-sm border-b border-gray-200 dark:border-gray-700">
                <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6">
                    @yield('header')
                </div>
            </header>
        @endif

        <!-- Alert Messages -->
        @if(session('success'))
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 pt-6">
                <div class="bg-green-50 dark:bg-green-900/50 border border-green-200 dark:border-green-800 text-green-800 dark:text-green-200 px-4 py-3 rounded-md mb-4">
                    {{ session('success') }}
                </div>
            </div>
        @endif

        @if(session('error'))
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 pt-6">
                <div class="bg-red-50 dark:bg-red-900/50 border border-red-200 dark:border-red-800 text-red-800 dark:text-red-200 px-4 py-3 rounded-md mb-4">
                    {{ session('error') }}
                </div>
            </div>
        @endif

        <!-- Page Content -->
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
            @yield('content')
        </div>
    </main>

    <!-- Footer -->
    @include('layouts.components.footer')

    <!-- Back to Top Button -->
    <button id="back-to-top" class="fixed bottom-8 right-8 bg-indigo-600 hover:bg-indigo-700 text-white p-3 rounded-full shadow-lg transition-all duration-300 opacity-0 invisible">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 10l7-7m0 0l7 7m-7-7v18"/>
        </svg>
    </button>

    <!-- Custom Scripts -->
    @stack('scripts')
    
    <script>
        // Hide loading spinner when page is loaded
        window.addEventListener('load', () => {
            document.getElementById('loading').style.display = 'none';
        });

        // Back to top functionality
        const backToTopButton = document.getElementById('back-to-top');
        window.addEventListener('scroll', () => {
            if (window.pageYOffset > 100) {
                backToTopButton.classList.remove('opacity-0', 'invisible');
            } else {
                backToTopButton.classList.add('opacity-0', 'invisible');
            }
        });

        backToTopButton.addEventListener('click', () => {
            window.scrollTo({ top: 0, behavior: 'smooth' });
        });
    </script>
</body>
</html>
```

---

## 🧭 2. Komponen Navigation

### `resources/views/layouts/components/navigation.blade.php`:
```blade
<nav class="bg-white dark:bg-gray-800 shadow-lg sticky top-0 z-40 border-b border-gray-200 dark:border-gray-700">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center h-16">
            <!-- Logo -->
            <div class="flex items-center">
                <a href="{{ route('home') }}" class="flex items-center space-x-2">
                    <div class="w-8 h-8 bg-gradient-to-r from-indigo-500 to-purple-600 rounded-lg flex items-center justify-center">
                        <span class="text-white font-bold text-sm">L</span>
                    </div>
                    <span class="text-xl font-bold text-gray-900 dark:text-white">
                        {{ config('app.name') }}
                    </span>
                </a>
            </div>

            <!-- Desktop Navigation -->
            <div class="hidden md:flex items-center space-x-8">
                <a href="{{ route('home') }}" class="nav-link {{ Request::routeIs('home') ? 'active' : '' }}">
                    Home
                </a>
                <a href="{{ route('about') }}" class="nav-link {{ Request::routeIs('about') ? 'active' : '' }}">
                    About
                </a>
                <a href="{{ route('contact') }}" class="nav-link {{ Request::routeIs('contact') ? 'active' : '' }}">
                    Contact
                </a>
            </div>

            <!-- Dark Mode Toggle & Mobile Menu -->
            <div class="flex items-center space-x-4">
                <!-- Dark Mode Toggle -->
                <button id="theme-toggle" class="p-2 rounded-lg bg-gray-100 dark:bg-gray-700 hover:bg-gray-200 dark:hover:bg-gray-600 transition-colors">
                    <svg id="theme-toggle-dark-icon" class="hidden w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                        <path d="M17.293 13.293A8 8 0 016.707 2.707a8.001 8.001 0 1010.586 10.586z"></path>
                    </svg>
                    <svg id="theme-toggle-light-icon" class="hidden w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                        <path d="M10 2L13.09 8.26L20 9L14 14.74L15.18 21.02L10 17.77L4.82 21.02L6 14.74L0 9L6.91 8.26L10 2Z"></path>
                    </svg>
                </button>

                <!-- Mobile menu button -->
                <button id="mobile-menu-button" class="md:hidden p-2 rounded-lg bg-gray-100 dark:bg-gray-700">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"/>
                    </svg>
                </button>
            </div>
        </div>

        <!-- Mobile Navigation -->
        <div id="mobile-menu" class="md:hidden hidden border-t border-gray-200 dark:border-gray-700">
            <div class="px-2 pt-2 pb-3 space-y-1">
                <a href="{{ route('home') }}" class="mobile-nav-link {{ Request::routeIs('home') ? 'active' : '' }}">
                    Home
                </a>
                <a href="{{ route('about') }}" class="mobile-nav-link {{ Request::routeIs('about') ? 'active' : '' }}">
                    About
                </a>
                <a href="{{ route('contact') }}" class="mobile-nav-link {{ Request::routeIs('contact') ? 'active' : '' }}">
                    Contact
                </a>
            </div>
        </div>
    </div>
</nav>

<style>
.nav-link {
    @apply text-gray-600 dark:text-gray-300 hover:text-indigo-600 dark:hover:text-indigo-400 px-3 py-2 rounded-md text-sm font-medium transition-colors duration-200;
}

.nav-link.active {
    @apply text-indigo-600 dark:text-indigo-400 bg-indigo-50 dark:bg-indigo-900/50;
}

.mobile-nav-link {
    @apply block px-3 py-2 rounded-md text-base font-medium text-gray-600 dark:text-gray-300 hover:text-indigo-600 dark:hover:text-indigo-400 hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors duration-200;
}

.mobile-nav-link.active {
    @apply text-indigo-600 dark:text-indigo-400 bg-indigo-50 dark:bg-indigo-900/50;
}
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
    // Mobile menu toggle
    const mobileMenuButton = document.getElementById('mobile-menu-button');
    const mobileMenu = document.getElementById('mobile-menu');
    
    mobileMenuButton.addEventListener('click', () => {
        mobileMenu.classList.toggle('hidden');
    });

    // Dark mode toggle
    const themeToggle = document.getElementById('theme-toggle');
    const darkIcon = document.getElementById('theme-toggle-dark-icon');
    const lightIcon = document.getElementById('theme-toggle-light-icon');
    
    // Check theme preference
    if (localStorage.getItem('color-theme') === 'dark' || (!('color-theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
        document.documentElement.classList.add('dark');
        darkIcon.classList.remove('hidden');
    } else {
        document.documentElement.classList.remove('dark');
        lightIcon.classList.remove('hidden');
    }
    
    themeToggle.addEventListener('click', () => {
        darkIcon.classList.toggle('hidden');
        lightIcon.classList.toggle('hidden');
        
        if (document.documentElement.classList.contains('dark')) {
            document.documentElement.classList.remove('dark');
            localStorage.setItem('color-theme', 'light');
        } else {
            document.documentElement.classList.add('dark');
            localStorage.setItem('color-theme', 'dark');
        }
    });
});
</script>
```

---

## 🦶 3. Komponen Footer

### `resources/views/layouts/components/footer.blade.php`:
```blade
<footer class="bg-gray-900 dark:bg-gray-950 text-gray-300">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
        <div class="grid grid-cols-1 md:grid-cols-4 gap-8">
            <!-- Brand -->
            <div class="md:col-span-2">
                <div class="flex items-center space-x-2 mb-4">
                    <div class="w-8 h-8 bg-gradient-to-r from-indigo-500 to-purple-600 rounded-lg flex items-center justify-center">
                        <span class="text-white font-bold text-sm">L</span>
                    </div>
                    <span class="text-xl font-bold text-white">{{ config('app.name') }}</span>
                </div>
                <p class="text-gray-400 mb-4 max-w-md">
                    Modern Laravel application built with Tailwind CSS v4, designed for performance and scalability.
                </p>
                <div class="flex space-x-4">
                    <a href="#" class="text-gray-400 hover:text-indigo-400 transition-colors">
                        <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                            <path d="M24 4.557c-.883.392-1.832.656-2.828.775 1.017-.609 1.798-1.574 2.165-2.724-.951.564-2.005.974-3.127 1.195-.897-.957-2.178-1.555-3.594-1.555-3.179 0-5.515 2.966-4.797 6.045-4.091-.205-7.719-2.165-10.148-5.144-1.29 2.213-.669 5.108 1.523 6.574-.806-.026-1.566-.247-2.229-.616-.054 2.281 1.581 4.415 3.949 4.89-.693.188-1.452.232-2.224.084.626 1.956 2.444 3.379 4.6 3.419-2.07 1.623-4.678 2.348-7.29 2.04 2.179 1.397 4.768 2.212 7.548 2.212 9.142 0 14.307-7.721 13.995-14.646.962-.695 1.797-1.562 2.457-2.549z"/>
                        </svg>
                    </a>
                    <a href="#" class="text-gray-400 hover:text-indigo-400 transition-colors">
                        <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                            <path d="M22.46 6c-.77.35-1.6.58-2.46.69.88-.53 1.56-1.37 1.88-2.38-.83.5-1.75.85-2.72 1.05C18.37 4.5 17.26 4 16 4c-2.35 0-4.27 1.92-4.27 4.29 0 .34.04.67.11.98C8.28 9.09 5.11 7.38 3 4.79c-.37.63-.58 1.37-.58 2.15 0 1.49.75 2.81 1.91 3.56-.71 0-1.37-.2-1.95-.5v.03c0 2.08 1.48 3.82 3.44 4.21a4.22 4.22 0 0 1-1.93.07 4.28 4.28 0 0 0 4 2.98 8.521 8.521 0 0 1-5.33 1.84c-.34 0-.68-.02-1.02-.06C3.44 20.29 5.7 21 8.12 21 16 21 20.33 14.46 20.33 8.79c0-.19 0-.37-.01-.56.84-.6 1.56-1.36 2.14-2.23z"/>
                        </svg>
                    </a>
                    <a href="#" class="text-gray-400 hover:text-indigo-400 transition-colors">
                        <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                            <path d="M12.017 0C5.396 0 .029 5.367.029 11.987c0 5.079 3.158 9.417 7.618 11.174-.105-.949-.199-2.403.041-3.439.219-.937 1.406-5.957 1.406-5.957s-.359-.72-.359-1.781c0-1.663.967-2.911 2.168-2.911 1.024 0 1.518.769 1.518 1.688 0 1.029-.653 2.567-.992 3.992-.285 1.193.6 2.165 1.775 2.165 2.128 0 3.768-2.245 3.768-5.487 0-2.861-2.063-4.869-5.008-4.869-3.41 0-5.409 2.562-5.409 5.199 0 1.033.394 2.143.889 2.741.197.261.225.489.165.757-.053.224-.172.687-.402 1.33-.026.111-.096.135-.221.08-1.235-.576-2.01-2.38-2.01-3.821 0-3.785 2.75-7.262 7.929-7.262 4.163 0 7.398 2.967 7.398 6.931 0 4.136-2.607 7.464-6.227 7.464-1.216 0-2.357-.631-2.75-1.378l-.748 2.853c-.271 1.043-1.002 2.35-1.492 3.146C9.57 23.812 10.763 24.009 12.017 24.009c6.624 0 11.99-5.367 11.99-11.988C24.007 5.367 18.641.001.012.001z"/>
                        </svg>
                    </a>
                </div>
            </div>

            <!-- Quick Links -->
            <div>
                <h3 class="text-white font-semibold mb-4">Quick Links</h3>
                <ul class="space-y-2">
                    <li><a href="{{ route('home') }}" class="text-gray-400 hover:text-indigo-400 transition-colors">Home</a></li>
                    <li><a href="{{ route('about') }}" class="text-gray-400 hover:text-indigo-400 transition-colors">About</a></li>
                    <li><a href="{{ route('contact') }}" class="text-gray-400 hover:text-indigo-400 transition-colors">Contact</a></li>
                    <li><a href="#" class="text-gray-400 hover:text-indigo-400 transition-colors">Privacy Policy</a></li>
                </ul>
            </div>

            <!-- Contact Info -->
            <div>
                <h3 class="text-white font-semibold mb-4">Contact</h3>
                <ul class="space-y-2 text-gray-400">
                    <li>📧 hello@example.com</li>
                    <li>📱 +62 123 456 789</li>
                    <li>📍 Barito Kuala, Indonesia</li>
                </ul>
            </div>
        </div>

        <div class="mt-8 pt-8 border-t border-gray-800 text-center text-gray-400">
            <p>&copy; {{ date('Y') }} {{ config('app.name') }}. All rights reserved.</p>
        </div>
    </div>
</footer>
```

---

## 📄 4. Contoh Penggunaan di Page

### `resources/views/pages/home.blade.php`:
```blade
@extends('layouts.app')

@section('description', 'Welcome to our modern Laravel application built with Tailwind CSS v4')
@section('keywords', 'laravel, tailwind, home, modern')

@section('header')
    <div class="text-center">
        <h1 class="text-4xl font-bold text-gray-900 dark:text-white mb-2">
            Welcome to Laravel
        </h1>
        <p class="text-lg text-gray-600 dark:text-gray-300">
            Modern web application built with Tailwind CSS v4
        </p>
    </div>
@endsection

@section('content')
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        <!-- Feature Cards -->
        <div class="bg-white dark:bg-gray-800 rounded-xl shadow-lg p-6 border border-gray-200 dark:border-gray-700">
            <div class="w-12 h-12 bg-indigo-100 dark:bg-indigo-900 rounded-lg flex items-center justify-center mb-4">
                <svg class="w-6 h-6 text-indigo-600 dark:text-indigo-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
                </svg>
            </div>
            <h3 class="text-xl font-semibold mb-2 text-gray-900 dark:text-white">Fast Performance</h3>
            <p class="text-gray-600 dark:text-gray-300">Built with modern tools for optimal performance and user experience.</p>
        </div>

        <div class="bg-white dark:bg-gray-800 rounded-xl shadow-lg p-6 border border-gray-200 dark:border-gray-700">
            <div class="w-12 h-12 bg-green-100 dark:bg-green-900 rounded-lg flex items-center justify-center mb-4">
                <svg class="w-6 h-6 text-green-600 dark:text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/>
                </svg>
            </div>
            <h3 class="text-xl font-semibold mb-2 text-gray-900 dark:text-white">Reliable</h3>
            <p class="text-gray-600 dark:text-gray-300">Tested and proven architecture with robust error handling.</p>
        </div>

        <div class="bg-white dark:bg-gray-800 rounded-xl shadow-lg p-6 border border-gray-200 dark:border-gray-700">
            <div class="w-12 h-12 bg-purple-100 dark:bg-purple-900 rounded-lg flex items-center justify-center mb-4">
                <svg class="w-6 h-6 text-purple-600 dark:text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"/>
                </svg>
            </div>
            <h3 class="text-xl font-semibold mb-2 text-gray-900 dark:text-white">User Friendly</h3>
            <p class="text-gray-600 dark:text-gray-300">Intuitive design that focuses on user experience and accessibility.</p>
        </div>
    </div>

    <div class="mt-12 text-center">
        <a href="{{ route('about') }}" class="inline-flex items-center px-6 py-3 bg-indigo-600 hover:bg-indigo-700 text-white font-medium rounded-lg transition-colors">
            Learn More
            <svg class="ml-2 w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 8l4 4m0 0l-4 4m4-4H3"/>
            </svg>
        </a>
    </div>
@endsection

@push('styles')
<style>
    .hero-gradient {
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    }
</style>
@endpush

@push('scripts')
<script>
    console.log('Home page loaded successfully!');
</script>
@endpush
```

---

## 🔧 5. Update CSS App untuk Styling Tambahan

### Tambahkan ke `resources/css/app.css`:
```css
@import 'tailwindcss';

/* Override dark mode behavior untuk class-based dark mode */
@custom-variant dark (&:where(.dark, .dark *));

/* Custom Components */
@layer components {
    .btn-primary {
        @apply px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white font-medium rounded-lg transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2;
    }
    
    .btn-secondary {
        @apply px-4 py-2 bg-gray-200 hover:bg-gray-300 dark:bg-gray-700 dark:hover:bg-gray-600 text-gray-900 dark:text-gray-100 font-medium rounded-lg transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2;
    }
    
    .card {
        @apply bg-white dark:bg-gray-800 rounded-xl shadow-lg border border-gray-200 dark:border-gray-700 overflow-hidden;
    }
    
    .input-field {
        @apply w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-lg bg-white dark:bg-gray-700 text-gray-900 dark:text-gray-100 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent;
    }
}

/* Custom Utilities */
@layer utilities {
    .text-gradient {
        @apply bg-gradient-to-r from-indigo-600 to-purple-600 bg-clip-text text-transparent;
    }
    
    .shadow-glow {
        box-shadow: 0 0 30px rgba(99, 102, 241, 0.3);
    }
}

/* Smooth transitions */
* {
    @apply transition-colors duration-200;
}

/* Custom scrollbar */
::-webkit-scrollbar {
    width: 8px;
}

::-webkit-scrollbar-track {
    @apply bg-gray-100 dark:bg-gray-800;
}

::-webkit-scrollbar-thumb {
    @apply bg-gray-400 dark:bg-gray-600 rounded-full;
}

::-webkit-scrollbar-thumb:hover {
    @apply bg-gray-500 dark:bg-gray-500;
}
```

---

## 📦 6. Route Configuration

### Tambahkan ke `routes/web.php`:
```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('pages.home');
})->name('home');

Route::get('/about', function () {
    return view('pages.about');
})->name('about');

Route::get('/contact', function () {
    return view('pages.contact');
})->name('contact');
```

---

## ✨ 7. Fitur Tambahan

### Dark Mode Support
Layout ini sudah include dark mode toggle yang akan:
- Menyimpan preferensi user di localStorage
- Mengikuti system preference secara default
- Smooth transition antar tema

### Accessibility Features
- Skip navigation link untuk screen readers
- Proper semantic HTML structure
- ARIA labels dan roles
- Keyboard navigation support
- High contrast colors

### Performance Optimizations
- Preconnect untuk Google Fonts
- Lazy loading untuk gambar (bisa ditambahkan)
- Minified CSS dan JS via Vite
- Optimized asset loading

---

## 🎯 8. Tips Penggunaan

### Menggunakan Layout
```blade
@extends('layouts.app')

@section('title', 'Page Title')
@section('description', 'Page description for SEO')

@section('header')
    <!-- Optional header content -->
@endsection

@section('content')
    <!-- Main page content -->
@endsection

@push('styles')
    <!-- Custom CSS untuk page ini -->
@endpush

@push('scripts')
    <!-- Custom JS untuk page ini -->
@endpush
```

### Menambahkan Alert Messages
```php
// Di Controller
return redirect()->route('home')->with('success', 'Data berhasil disimpan!');
return redirect()->back()->with('error', 'Terjadi kesalahan!');
```

### Custom Components
Buat komponen reusable di `resources/views/components/`:

```blade
<!-- resources/views/components/button.blade.php -->
@props(['variant' => 'primary', 'size' => 'md', 'type' => 'button'])

@php
$classes = [
    'primary' => 'btn-primary',
    'secondary' => 'btn-secondary',
];

$sizes = [
    'sm' => 'px-3 py-1.5 text-sm',
    'md' => 'px-4 py-2',
    'lg' => 'px-6 py-3 text-lg',
];
@endphp

<button 
    type="{{ $type }}"
    {{ $attributes->merge(['class' => $classes[$variant] . ' ' . $sizes[$size]]) }}
>
    {{ $slot }}
</button>
```

Penggunaan:
```blade
<x-button variant="primary" size="lg">Save Data</x-button>
<x-button variant="secondary" onclick="closeModal()">Cancel</x-button>
```

---

## 🚀 9. Customization Options

### Mengubah Color Scheme
Edit `tailwind.config.js`:
```js
export default {
  content: [
    './resources/views/**/*.blade.php',
    './resources/js/**/*.{js,vue,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        },
        // Custom colors
        brand: {
          50: '#f0f9ff',
          500: '#0ea5e9',
          600: '#0284c7',
        }
      },
    },
  },
  plugins: [],
}
```

### Menambahkan Animation
```css
@layer components {
    .fade-in {
        animation: fadeIn 0.5s ease-in-out;
    }
    
    .slide-up {
        animation: slideUp 0.3s ease-out;
    }
}

@keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
}

@keyframes slideUp {
    from { 
        opacity: 0;
        transform: translateY(20px);
    }
    to { 
        opacity: 1;
        transform: translateY(0);
    }
}
```

---

## 🔧 10. Troubleshooting Layout

### Layout tidak muncul
1. Pastikan route menggunakan `return view('pages.home')` bukan `welcome`
2. Cek apakah file layout ada di `resources/views/layouts/app.blade.php`
3. Pastikan `@extends('layouts.app')` di awal file view

### Dark mode tidak berfungsi
1. Cek apakah JavaScript untuk dark mode toggle sudah dimuat
2. Pastikan Tailwind config sudah include `darkMode: 'class'`
3. Cek browser console untuk error JavaScript

### Styling tidak muncul
1. Jalankan `npm run dev` atau `npm run build`
2. Pastikan `@vite` directive ada di layout
3. Clear browser cache
4. Cek apakah custom CSS sudah ditambahkan ke `app.css`

---

## 📱 11. Mobile Responsiveness

Layout ini sudah responsive dengan breakpoints:
- `sm:` - Small screens (640px+)
- `md:` - Medium screens (768px+)
- `lg:` - Large screens (1024px+)
- `xl:` - Extra large screens (1280px+)

### Testing Responsiveness
```bash
# Jalankan dev server
npm run dev

# Test di berbagai device sizes:
# - Mobile: 375px
# - Tablet: 768px
# - Desktop: 1024px+
```

---

## 🎨 12. Design System

### Typography Scale
```css
.text-xs    /* 12px */
.text-sm    /* 14px */
.text-base  /* 16px */
.text-lg    /* 18px */
.text-xl    /* 20px */
.text-2xl   /* 24px */
.text-3xl   /* 30px */
.text-4xl   /* 36px */
```

### Spacing Scale
```css
.p-1   /* 4px */
.p-2   /* 8px */
.p-4   /* 16px */
.p-6   /* 24px */
.p-8   /* 32px */
.p-12  /* 48px */
```

### Color Palette
- **Primary**: Indigo (600, 700)
- **Secondary**: Gray (200, 300, 700, 800)
- **Success**: Green (50, 600, 800)
- **Error**: Red (50, 600, 800)

---

## 📚 13. Next Steps

1. **Buat Component Library**: Kembangkan komponen reusable
2. **Add Authentication**: Integrate dengan Laravel Breeze/Jetstream
3. **Form Handling**: Tambahkan form validation dan handling
4. **Data Tables**: Implement sortable, filterable tables
5. **API Integration**: Connect dengan backend APIs
6. **Testing**: Tambahkan unit dan feature tests

---

## 🤝 14. Contributing

Untuk berkontribusi pada layout ini:

1. Fork repository
2. Buat feature branch (`git checkout -b feature/new-component`)
3. Commit changes (`git commit -am 'Add new component'`)
4. Push to branch (`git push origin feature/new-component`)
5. Create Pull Request

---

## 📖 15. Resources

- [Laravel Documentation](https://laravel.com/docs)
- [Tailwind CSS v4 Docs](https://tailwindcss.com/docs)
- [Blade Templates](https://laravel.com/docs/blade)
- [Vite Laravel Plugin](https://laravel.com/docs/vite)

---

> 💡 **Pro Tip**: Simpan struktur ini sebagai template untuk project Laravel baru. Anda bisa membuat Artisan command untuk generate layout otomatis!

## 🎉 Selamat!

Layout system yang robust dan modern sudah siap digunakan. Dengan struktur ini, Anda dapat:

- Membangun aplikasi yang scalable
- Maintain consistency dalam design
- Mengembangkan fitur dengan lebih cepat
- Memberikan UX yang excellent

**Happy coding!** 🚀
