<style>
header {
  background: black;
  width: 100%;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 20;
}

@media screen and (max-width: 1200px) {
  header {
    position: relative;
  }
}

.navbar {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  /* position: fixed;
  width: 100%;
  left: 0;
  top: 0;
  transition: ease 0.4s;
  &--scrolled {
    height: 40px;
  } */
}

.nav-link {
  font-weight: bold;
  font-size: 14px;
  text-transform: uppercase;
  text-decoration: none;
  color: #ffffff;
  padding: 20px 0px;
  margin: 0px 20px;
  display: inline-block;
  position: relative;
  opacity: 0.75;
}

.nav-link:hover {
  opacity: 1;
}

.nav-link::before {
  transition: 300ms;
  height: 5px;
  content: "";
  position: absolute;
  background-color: #FF6600;
}

.nav-link-ltr::before {
  width: 0%;
  bottom: 10px;
}

.nav-link-ltr:hover::before {
  width: 100%;
}

</style>

<header>
  <div class="navbar">
    <a class="nav-link nav-link-ltr" href="/">Home</a>
    <a class="nav-link nav-link-ltr" href="#who">Who</a>
    <a class="nav-link nav-link-ltr" href="#when">When</a>
    <a class="nav-link nav-link-ltr" href="#where">Where</a>
    <a class="nav-link nav-link-ltr" href="#what">What</a>
  </div>
</header>
