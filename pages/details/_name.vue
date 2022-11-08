<template>
  <div class="col-lg-12 col-sm-12">
    <button
      type="button"
      class="btn btn-outline-primary m-1"
      style="width: 33%"
      @click.prevent="goToPrev()"
    >
      <font-awesome-icon icon="fa-solid fa-arrow-left" /> Back to results
    </button>

    <div class="container-fluid">
      <!-- <div class="row">
        <div class="col-md-12">
          <nav class="navbar navbar-expand-lg navbar-light bg-light">
            <button
              class="navbar-toggler"
              type="button"
              data-toggle="collapse"
              data-target="#bs-example-navbar-collapse-1"
            >
              <span class="navbar-toggler-icon"></span>
            </button>
            <a class="navbar-brand" href="#">Brand</a>
            <div
              class="collapse navbar-collapse"
              id="bs-example-navbar-collapse-1"
            >
              <ul class="navbar-nav">
                <li class="nav-item active">
                  <a class="nav-link" href="#"
                    >Link <span class="sr-only">(current)</span></a
                  >
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="#">Link</a>
                </li>
                <li class="nav-item dropdown">
                  <a
                    class="nav-link dropdown-toggle"
                    href="http://example.com"
                    id="navbarDropdownMenuLink"
                    data-toggle="dropdown"
                    >Dropdown link</a
                  >
                  <div
                    class="dropdown-menu"
                    aria-labelledby="navbarDropdownMenuLink"
                  >
                    <a class="dropdown-item" href="#">Action</a>
                    <a class="dropdown-item" href="#">Another action</a>
                    <a class="dropdown-item" href="#">Something else here</a>
                    <div class="dropdown-divider"></div>
                    <a class="dropdown-item" href="#">Separated link</a>
                  </div>
                </li>
              </ul>
              <form class="form-inline">
                <input class="form-control mr-sm-2" type="text" />
                <button class="btn btn-primary my-2 my-sm-0" type="submit">
                  Search
                </button>
              </form>
              <ul class="navbar-nav ml-md-auto">
                <li class="nav-item active">
                  <a class="nav-link" href="#"
                    >Link <span class="sr-only">(current)</span></a
                  >
                </li>
                <li class="nav-item dropdown">
                  <a
                    class="nav-link dropdown-toggle"
                    href="http://example.com"
                    id="navbarDropdownMenuLink"
                    data-toggle="dropdown"
                    >Dropdown link</a
                  >
                  <div
                    class="dropdown-menu dropdown-menu-right"
                    aria-labelledby="navbarDropdownMenuLink"
                  >
                    <a class="dropdown-item" href="#">Action</a>
                    <a class="dropdown-item" href="#">Another action</a>
                    <a class="dropdown-item" href="#">Something else here</a>
                    <div class="dropdown-divider"></div>
                    <a class="dropdown-item" href="#">Separated link</a>
                  </div>
                </li>
              </ul>
            </div>
          </nav>
        </div>
      </div> -->
      <div class="row">
        <div class="col-md-12">
          <div class="page-header">
            <h1>{{ this.$route.params.name.replaceAll("-", " ") }}</h1>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-6">
          <h3>Resource Abstract</h3>
          <p>
            Donec id elit non mi porta gravida at eget metus. Fusce dapibus,
            tellus ac cursus commodo, tortor mauris condimentum nibh, ut
            fermentum massa justo sit amet risus. Etiam porta sem malesuada
            magna mollis euismod. Donec sed odio dui.
          </p>
        </div>
        <div class="col-md-6"></div>
      </div>
      <!-- <div class="row">
        <div class="col-md-6">
          <div id="card-142574">
            <div class="card">
              <div class="card-header">
                <a
                  class="card-link"
                  data-toggle="collapse"
                  data-parent="#card-142574"
                  href="#card-element-272589"
                  >Collapsible Group Item #1</a
                >
              </div>
              <div id="card-element-272589" class="collapse show">
                <div class="card-body">Anim pariatur cliche...</div>
              </div>
            </div>
            <div class="card">
              <div class="card-header">
                <a
                  class="collapsed card-link"
                  data-toggle="collapse"
                  data-parent="#card-142574"
                  href="#card-element-232080"
                  >Collapsible Group Item #2</a
                >
              </div>
              <div id="card-element-232080" class="collapse">
                <div class="card-body">Anim pariatur cliche...</div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-6">
          <div id="card-545010">
            <div class="card">
              <div class="card-header">
                <a
                  class="card-link"
                  data-toggle="collapse"
                  data-parent="#card-545010"
                  href="#card-element-949363"
                  >Collapsible Group Item #1</a
                >
              </div>
              <div id="card-element-949363" class="collapse show">
                <div class="card-body">Anim pariatur cliche...</div>
              </div>
            </div>
            <div class="card">
              <div class="card-header">
                <a
                  class="collapsed card-link"
                  data-toggle="collapse"
                  data-parent="#card-545010"
                  href="#card-element-474866"
                  >Collapsible Group Item #2</a
                >
              </div>
              <div id="card-element-474866" class="collapse">
                <div class="card-body">Anim pariatur cliche...</div>
              </div>
            </div>
          </div>
        </div>
      </div> -->
    </div>
  </div>
</template>
<script>
import Vue from "vue";
import "bootstrap/dist/css/bootstrap.css";
import { library } from "@fortawesome/fontawesome-svg-core";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { faArrowLeft } from "@fortawesome/free-solid-svg-icons";
library.add(faArrowLeft);
Vue.component("font-awesome-icon", FontAwesomeIcon);
Vue.config.productionTip = false;

export default Vue.extend({
  name: "Dataset",
  methods: {
    goToPrev() {
      this.$router.go(-1);
      // window.location.href = "/";
    },
  },
  created() {
    console.log(this.$route.params);
  },
  mounted() {},
});
</script>
