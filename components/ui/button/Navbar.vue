<script setup lang="ts"></script>

<template>
  <div class="navbar">
    <div class="navbar__logo">
      <NuxtLink to="/">
        <img src="~/assets/logo.svg" alt="Logo" />
      </NuxtLink>
    </div>
    <div class="navbar__menu">
      <NuxtLink to="/dashboard">Dashboard</NuxtLink>
      <NuxtLink to="/about">About</NuxtLink>
      <NuxtLink to="/contact">Contact</NuxtLink>
    </div>
  </div>
</template>
