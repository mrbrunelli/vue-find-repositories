<template>
  <div class="container">
    <div class="text-center mt-5 mb-5">
      <div
        v-show="alertIsVisible"
        class="alert alert-danger"
        role="alert"
      >
        <h4 class="alert-heading">
          Erro ao buscar repositórios
        </h4>
        <p>Verifique se o nome de usuário foi digitado corretamente</p>
      </div>
      <h1 class="display-4 text-muted">
        Pesquisador de Repositórios GitHub
      </h1>
      <div class="card">
        <div class="card-body">
          <div class="input-group">
            <input
              v-model="userName"
              type="text"
              class="form-control"
              placeholder="Digite o nome do usuário"
              autofocus
              @keyup.enter="getReposByUserName"
            >
            <div class="input-group-append">
              <button
                class="btn btn-primary"
                :disabled="isLoading || !userName"
                @click="getReposByUserName"
              >
                <span>
                  Pesquisar
                  <div
                    v-show="isLoading"
                    class="spinner-border spinner-border-sm"
                    role="status"
                  >
                    <span class="sr-only">Carregando...</span>
                  </div>
                </span>
              </button>
            </div>
          </div>
        </div>
      </div>
      <div class="text-right mt-3 mb-1">
        <span class="badge badge-primary">{{ repos.length }}</span>
      </div>
      <div class="card">
        <div class="card-body">
          <div class="table-responsive">
            <table class="table table-hover text-left">
              <thead>
                <tr>
                  <th scope="col">
                    Nome
                  </th>
                  <th scope="col">
                    Descrição
                  </th>
                  <th scope="col">
                    Link
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="repo in repos"
                  :key="repo.id"
                >
                  <td scope="row">
                    {{ repo.name }}
                  </td>
                  <td scope="row">
                    {{ repo.description }}
                  </td>
                  <td scope="row">
                    <a
                      :href="repo.html_url"
                      target="_blank"
                    > Acessar </a>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
          <div
            v-show="isLoading"
            class="spinner-border text-primary"
            role="status"
          >
            <span class="sr-only">Carregando...</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css"

export default {
  name: "App",
  data() {
    return {
      repos: [],
      userName: "",
      isLoading: false,
      alertIsVisible: false
    }
  },
  methods: {
    async getReposByUserName() {
      this.isLoading = true
      this.alertIsVisible = false
      try {
        const config = "sort=interactions&per_page=30"
        const response = await fetch(
          `https://api.github.com/users/${this.userName}/repos?${config}`
        )
        const data = await response.json()
        this.repos.splice(0, this.repos.length)
        this.repos.push(...data)
      } catch (error) {
        this.alertIsVisible = true
      } finally {
        setTimeout(() => {
          this.isLoading = false
        }, 500)
      }
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  height: 90vh;
  background-color: #f8f9fa;
}
</style>
