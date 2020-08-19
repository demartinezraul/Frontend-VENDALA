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
                      <th class="text-right">Actions</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr v-for="kit in kits" :key="kit.id">
                      <td class="text-center">{{ kit.id }}</td>
                      <td>{{ kit.name }}</td>
                      <td class="td-actions text-right">
                        <button type="button" rel="tooltip" class="btn btn-danger btn-icon btn-sm"
                                data-original-title=""
                                title=""
                                v-on:click="destroy(kit.id)">
                          <i class="ni ni-fat-remove pt-1"></i>
                        </button>
                      </td>
                    </tr>
                    <tr v-if="!kits.length" class="text-center">
                      <td colspan="3">No Records Found.</td>
                    </tr>
                    </tbody>
                  </table>
                </div>
                <paginator ref="paginator"
                           :origem="'kits'"
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
  name: 'kits',
  components: {
    Paginator
  },
  data() {
    return {
      resource_url: `${app.service.api}/api/kits`,
      kits: [],
      loading: true,
    };
  },
  mounted() {
    axios
        .get(`${app.service.api}/api/kits`, {headers: http.header()})
        .then(response => {
          this.kits = response.data.data;
        })
        .catch(error => {
          console.log(error)
        })
        .finally(() => this.loading = false)
  },

  methods: {
    updateResource(kits) {
      this.kits = kits.data;
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
          this.$http.delete(`${app.service.api}/api/kits/destroy/${id}`, {headers: http.header()})
              .then((response) => {
                this.$refs.paginator.fetchData();
              }).catch((response) => {
            const processed = this.$response.process(response);
            this.$swal.fire(
                '',
                processed.message,
                'success',
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
