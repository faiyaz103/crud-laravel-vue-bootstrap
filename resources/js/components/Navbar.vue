<template>
    <nav class="navbar navbar-expand-lg bg-dark navbar-dark py-3 sticky-top">
        <div class="container">

            <!-- Logo -->
            <a href="#" class="navbar-brand">CRUD</a>

            <!-- hamburger menu -->
            <button
                class="navbar-toggler"
                type="button"
                data-bs-toggle="collapse"
                data-bs-target="#navmenu"
            >
                <span class="navbar-toggler-icon"></span>
            </button>

            <!-- for collapsing when screen is small than lg -->
            <div class="collapse navbar-collapse" id="navmenu">
                <ul class="navbar-nav">

                    <li class="navbar-item">
                        <router-link to="/" class="nav-link">Home</router-link>
                    </li>
                    <li class="navbar-item">
                        <router-link to="/posts" class="nav-link">Posts</router-link>
                    </li>

                </ul>
            </div>

        </div>
    </nav>
</template>