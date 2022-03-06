<template>
  <div class="header">
    <b-navbar toggleable="lg" type="dark">
      <img src="/logo.png" class="d-inline-block align-top" alt="Kitten">
      <b-navbar-brand href="#">Kanji</b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item href="#">Link</b-nav-item>
        </b-navbar-nav>

      </b-collapse>
    </b-navbar>
  </div>
</template>

<style>
  .header {
    background-color: #6200EE;
  }
</style>