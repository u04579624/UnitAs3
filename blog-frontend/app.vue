<template>
  <div>
    <header>
      <h1>My Blog</h1>
      <nav>
        <NuxtLink to="/">Home</NuxtLink>
        <NuxtLink to="/search">Search</NuxtLink>
      </nav>
    </header>
    <main>
      <NuxtPage />
    </main>
  </div>
</template>
<style>
header {
  padding: 1rem;
  background-color: #f8f8f8;
  text-align: center;
}
nav a {
  margin: 0 1rem;
  text-decoration: none;
  color: #007bff;
}
main {
  padding: 1rem;
}
</style>