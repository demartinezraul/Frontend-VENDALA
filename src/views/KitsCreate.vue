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
                      <h3 class="mb-0">New Kit</h3>
                    </div>
                  </div>
                </div>
                <div class="col-12">
                  <div class="form-group ">
                    <label>Name</label>
                    <input type="text" class="form-control" id="name" v-model="data.name">
                  </div>
                  <div class="form-group ">
                    <label>Products</label>
                    <div v-if="loading">Carregando...</div>
                    <multiselect
                        v-model="data.selections"
                        :options="products"
                        label="name"
                        track-by="name"
                        :hide-selected="true"
                        :close-on-select="false"
                        :searchable="true"
                        :multiple="true"
                        :custom-label="customLabel"
                        placeholder="Selections products">
                    </multiselect>
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

import axios from "axios";
import app from '@/config/app';
import http from '@/config/http';
import Multiselect from 'vue-multiselect'

export default {
  name: 'kitsCreate',
  components: {
    Multiselect,
  },
  data() {
    return {
      loading: true,
      data: {
        name: '',
        selections: [],
        products: [],
      },
      kits: [],
      products: [],
    }
  },
  mounted() {
    axios
        .get(`${app.service.api}/api/products`, {headers: http.header()})
        .then(response => {
          this.products = response.data.data;
        })
        .catch(error => {
          alert(error)
        })
        .finally(() => this.loading = false)
  },
  methods: {
    create() {
      try {
        let dados = this.data.selections;
        let products = [];
        for (var i=0; i<dados.length; i++) {
          products[i] = dados[i].id;
        }
        this.data.products = products;
        this.$http.post(`${app.service.api}/api/kits/store`, this.data,{headers: http.header()})
            .then((response) => {
              const processed = this.$response.process(response);
              this.$swal.fire(
                  '',
                  processed.message,
                  'success',
              );

              if (processed.data.success) {
                this.$router.push({ name: 'kits' });
              }
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
    customLabel ({ id, name }) {
      return `#${id} – ${name}`
    },
  }
};
</script>
<style></style>
