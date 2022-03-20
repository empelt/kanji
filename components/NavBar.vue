<template>
  <div class="header">
    <div class="header_content">
    <b-navbar toggleable="lg" type="dark">
      <img src="/logo.png" class="d-inline-block align-top logo" alt="Kitten">
      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item href="#">Link</b-nav-item>
        </b-navbar-nav>

      </b-collapse>
    </b-navbar>
    </div>
  </div>
</template>

<style>
  .header {
    background-color: #feeae8;
  }
  .header_content{
    max-width:1140px;
    margin: 0 auto;
  }
  .logo {
    width: 93px;
  }
  
</style>