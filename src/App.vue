<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="col-4">
          <h3>Nome do produto</h3>
          <input
            type="text"
            class="form-control"
            v-model="novoNome"
            ref="novoNome"
            :placeholder="placeholders.Nome"
          />
        </div>
        <div class="col-4">
          <h3>Descricao do produto</h3>
          <input
            type="text"
            class="form-control"
            v-model="novaDescricao"
            ref="novoNome"
            :placeholder="placeholders.Descrição"
          />
        </div>
        <div class="col-4">
          <h3>Preco do produto</h3>
          <input
            type="text"
            class="form-control"
            v-model="novoPreco"
            ref="novoNome"
            :placeholder="placeholders.Preço"
          />
        </div>
      </div>
      <br />
      <div class="row">
        <div class="col-4">
          <button type="button" class="btn btn-info" @click="addProduto">
            Adicionar Produto
          </button>
        </div>
        <div class="col-4" v-if="deletar == false">
          <button type="button" class="btn btn-danger" @click="deletar = true">
            Deletar produto
          </button>
        </div>
        <div class="col-4" v-else>
          <h3>Digite o codigo de produto</h3>
          <input type="text" v-model="idProduto" />
          <button
            type="button"
            class="btn btn-success ml-3"
            @click="deletarProduto"
          >
            Confirmar
          </button>
        </div>
        <div class="col-4" v-if="editar == false">
          <button
            type="button"
            class="btn btn-warning"
            @click="
              editar = true;
              atualizarPlaceholder();
            "
          >
            Atualizar produto
          </button>
        </div>
        <div class="col-4" v-else>
          <h3>Digite o código do produto</h3>
          <input type="text" v-model="idProduto" />
          <button
            type="button"
            class="btn btn-success ml-3"
            @click="editarProduto"
          >
            Confirmar
          </button>
        </div>
      </div>
      <br />
      <div>
        <table class="table table-dark">
          <thead>
            <tr>
              <th scope="col">Codigo de produto</th>
              <th scope="col">Nome</th>
              <th scope="col">Descricao</th>
              <th scope="col">Preco</th>
            </tr>
          </thead>
          <tbody v-for="produto in produtos" :key="produto._id">
            <tr class="linhas">
              <td>{{ produto._id }}</td>
              <td>{{ produto.titulo }}</td>
              <td>{{ produto.desc }}</td>
              <td>{{ produto.preco }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "app",
  data() {
    return {
      novoNome: "",
      novaDescricao: "",
      novoPreco: "",
      produtos: [],
      idProduto: "",
      editar: false,
      deletar: false,
      placeholders: {
        Nome: "Digite o nome do produto",
        Descrição: "Digite a descrição do produto",
        Preço: "Digite o preço do produto",
      },
    };
  },

  methods: {
    addProduto: function() {
      if (!this.novoNome || !this.novaDescricao || !this.novoPreco) {
        console.log("Escreve alguma coisa");
      } else {
        axios
          .post("http://localhost:4000/produtos", {
            titulo: `${this.novoNome}`,
            desc: `${this.novaDescricao}`,
            preco: `${this.novoPreco}`,
          })
          .then((response) => {
            this.produtos = [response.data, ...this.produtos];
            this.novoNome = "";
            this.novaDescricao = "";
            this.novoPreco = "";
            this.$refs.novoNome.focus();
          })
          .catch((err) => {
            console.log("deu BO");
          });
      }
    },
    atualizarPlaceholder: function() {
      this.placeholders = {
        Nome: "Digite o NOVO nome do produto",
        Descrição: "Digite a NOVA descrição do produto",
        Preço: "Digite o NOVO preço do produto",
      };
    },
    editarProduto: function() {
      if (
        !this.novoNome ||
        !this.novaDescricao ||
        !this.novoPreco ||
        !this.idProduto
      ) {
        console.log("Escreve alguma coisa");
      } else {
        axios
          .put(`http://localhost:4000/produtos/${this.idProduto}`, {
            titulo: `${this.novoNome}`,
            desc: `${this.novaDescricao}`,
            preco: `${this.novoPreco}`,
          })
          .then((response) => {
            this.produtos = [response.data, ...this.produtos];
            this.novoNome = "";
            this.novaDescricao = "";
            this.novoPreco = "";
            this.idProduto = "";
            this.$refs.novoNome.focus();
            this.editar = false;
            this.$router.go();
          })
          .catch((err) => {
            console.log("Deu problema");
          });
      }
    },
    deletarProduto: function() {
      if (!this.idProduto) {
        console.log("Escreve alguma coisa");
      } else {
        axios
          .delete(`http://localhost:4000/produtos/${this.idProduto}`)
          .then((response) => {
            this.idProduto = "";
            this.deletar = false;
            this.$router.go();
          })
          .catch((err) => {
            console.log("Id nao encontrado");
          });
      }
    },
  },
  created: function() {
    axios.get("http://localhost:4000/produtos").then((response) => {
      this.produtos = response.data;
    });
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Proza+Libre|Fira+Mono");
#app {
  font-family: "Proza Libre";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  background-color: #5b27c9;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
