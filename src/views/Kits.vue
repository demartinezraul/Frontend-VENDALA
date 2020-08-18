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
    <section class="section section-skew">
      <div class="container">
        <div class="container-fluid">
          <div class="row">
            <div class="col-12">
              <div class="card shadow">
                <div class="card-header bg-white border-0">
                  <div class="row align-items-center">
                    <div class="col-8">
                      <h3 class="mb-0">Kits</h3>
                    </div>
                    <div class="col-4 text-right">
                      <router-link :to="{ name: 'kitsCreate'}" class="btn btn-primary">
                        New kit
                      </router-link>
                    </div>
                  </div>
                </div>
                <div v-if="loading">Carregando...</div>
                <div class="table-responsive">
                  <table class="table">
                    <thead>
                    <tr>
                      <th class="text-center">#</th>
                      <th>Name</th>
                      <th>Description</th>
                      <th>Price</th>
                      <th class="text-right">Category</th>
                      <th class="text-right">Actions</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr v-for="product in products" :key="product.id">
                      <td class="text-center">{{ product.id }}</td>
                      <td>{{ product.name }}</td>
                      <td>{{ product.description }}</td>
                      <td>{{ product.price }}</td>
                      <td class="text-right">{{ product.category.name }}</td>
                      <td class="td-actions text-right">
                        <button type="button" rel="tooltip" class="btn btn-danger btn-icon btn-sm"
                                data-original-title=""
                                title=""
                                v-on:click="destroy(product.id)">
                          <i class="ni ni-fat-remove pt-1"></i>
                        </button>
                      </td>
                    </tr>
                    </tbody>
                  </table>
                </div>
                <paginator ref="paginator"
                           :origem="'products'"
                           :resource_url="resource_url"
                           @update="updateResource"></paginator>
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
import Paginator from '@/components/Paginator';

export default {
  name: 'products',
  components: {
    Paginator
  },
  data() {
    return {
      resource_url: `${app.service.api}/api/products`,
      products: [],
      loading: true,
    };
  },
  mounted() {
    axios
        .get(`${app.service.api}/api/products`, {headers: http.header()})
        .then(response => {
          this.products = response.data.data;
        })
        .catch(error => {
          console.log(error)
        })
        .finally(() => this.loading = false)
  },

  methods: {
    updateResource(products) {
      this.products = products.data;
    },

    async destroy(id) {
      const {value: result} = await this.$swal.fire({
        title: 'Confirm deletion?',
        text: '',
        icon: 'warning',
        showCancelButton: true,
        confirmButtonText: 'Sim',
        cancelButtonText: 'Não',
      });
      if (result === true) {
        try {
          this.$http.delete(`${app.service.api}/api/products/destroy/${id}`, {headers: http.header()})
              .then((response) => {
                this.$refs.paginator.fetchData();
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
      }
    }
  }
};
</script>
<style></style>
