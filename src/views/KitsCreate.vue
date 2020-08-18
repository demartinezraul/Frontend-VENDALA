<template>
  <div class="profile-page">
    <section class="section-profile-cover section-shaped my-0">
      <div class="shape shape-style-1 shape-primary shape-skew alpha-4">
        <span></span>
        <span></span>
        <span></span>
        <span></span>
        <span></span>
        <span></span>
        <span></span>
      </div>
    </section>
    <section class="section section-skew" style="padding-bottom: 20rem;">
      <div class="container">
        <div class="container-fluid">
          <div class="row">
            <div class="col-12">
              <div class="card shadow">
                <div class="card-header bg-white border-0">
                  <div class="row align-items-center">
                    <div class="col-8">
                      <div v-if="loading">Carregando...</div>
                      <h3 class="mb-0">New Kit</h3>
                    </div>
                  </div>
                </div>
                <div class="col-12">
                  <div class="form-group ">
                    <label>Name</label>
                    <input type="text" class="form-control">
                  </div>
                  <div class="form-group ">
                    <label>Products</label>
                    <select class="form-control">
                      <option>--</option>
                    </select>
                  </div>
                  <div class="form-group text-right">
                    <router-link :to="{ name: 'kits' }" class="btn btn-link">
                      <i class="zmdi zmdi-user"></i> Voltar</router-link>
                    <button type="button" name="button"
                            class="btn btn-primary"
                            v-on:click="create()">
                      Enviar
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>
<script>

import app from '@/config/app';
import http from '@/config/http';

export default {
  name: 'kitsCreate',
  data() {
    return {
      loading: true,
      kits: []
    }
  },

  methods: {
    create() {
      try {
        this.$http.post(`${app.service.api}/api/kits/store`, {headers: http.header()})
            .then((response) => {
              // TODO: Continue process;
            }).catch((response) => {
          const processed = this.$response.process(response);
          this.$swal.fire(
              'Oops!',
              processed.message,
              'error',
          );
        });
      } catch (error) {
        const processed = this.$response.process(error.response);
        this.$swal.fire(
            'Oops!',
            processed.message,
            'error',
        );
      }
    },
  }
};
</script>
<style></style>
