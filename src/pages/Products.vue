<template>
    <v-container>
      <!-- criando um botão para abrir o modal -->
      <v-row>
        <v-col class="d-flex justify-end">
          <v-btn color="primary" @click="openDialog">
            Cadastrar Produto
          </v-btn>
        </v-col>
      </v-row>
  
      <!-- criar a tabela de produtos -->
      <v-row>
        <v-col>
          <div class="table-container">
            <v-table>
              <thead>
                <tr>
                  <th>Nome</th>
                  <th>Preço</th>
                  <th>Ações</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(product, index) in products" :key="product._id">
                  <td>{{ product.nome }}</td>
                  <td>{{ product.preco }}</td>
                  <td>
                    <v-btn icon @click="editProduct(product)">
                      <v-icon>mdi-pencil</v-icon>
                    </v-btn>
                    <v-btn icon @click="deleteProduct(product._id)">
                      <v-icon>mdi-delete</v-icon>
                    </v-btn>
                  </td>
                </tr>
              </tbody>
            </v-table>
          </div>
        </v-col>
      </v-row>
  
      <!-- criando modal -->
      <v-dialog v-model="dialog">
        <v-card>
          <v-card-title>
            {{ isEdit ? 'Editar Produto' : 'Cadastrar Produto' }}
          </v-card-title>
          <v-card-text>
            <v-form>
              <v-text-field label="Nome do produto" v-model="productName"></v-text-field>
              <v-text-field label="Preço do produto" v-model="productPrice"></v-text-field>
            </v-form>
          </v-card-text>
          <v-card-actions>
              <v-btn color="blue darken-1" @click="saveProduct">{{ isEdit ? 'Salvar' : 'Adicionar' }}</v-btn>
              <v-btn color="blue darken-1" @click="dialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-container>
  </template>
  
  <script setup>
  import { ref, onMounted } from "vue";
  import axios from "axios";
  
  // definindo os tipos das variáveis
  const dialog = ref(false);
  const isEdit = ref(false);
  const products = ref([]);
  const productName = ref("");
  const productPrice = ref("");
  const editId = ref(null);
  
  // funçao para listagem dos produtos
  const getProducts = async () => {
    try {
      const response = await axios.get(`https://cruddesafio-cruddesafios-projects.vercel.app/products`);
      products.value = response.data;
    } catch (error) {
      console.error('Erro ao buscar produtos', error);
    }
  };
  
  // funçao para deleçao dos produtos
  const deleteProduct = async (id) => {
    try {
      await axios.delete(`https://cruddesafio-cruddesafios-projects.vercel.app/products/${id}`);
      getProducts();
    } catch (error) {
      console.error('Erro ao deletar produto', error);
    }
  };
  
  // funçao para salvar e editar os produtos
  const saveProduct = async () => {
    try {
      if (isEdit.value) {
        await axios.put(`https://cruddesafio-cruddesafios-projects.vercel.app/products/${editId.value}`, {
          nome: productName.value,
          preco: productPrice.value
        });
      } else {
        await axios.post(`https://cruddesafio-cruddesafios-projects.vercel.app/products`, {
          nome: productName.value,
          preco: productPrice.value
        });
      }
      getProducts();
      dialog.value = false;
      productName.value = '';
      productPrice.value = '';
      editId.value = null;
    } catch (error) {
      console.error('Erro ao salvar produto', error);
    }
  };
  
  // funçao para abrir o modal de edit com os dados do produto 
  const editProduct = (product) => {
    isEdit.value = true;
    productName.value = product.nome;
    productPrice.value = product.preco;
    editId.value = product._id;
    dialog.value = true;
  };
  
  // funçao para abrir o modal de cadastro
  const openDialog = () => {
    isEdit.value = false;
    productName.value = '';
    productPrice.value = '';
    dialog.value = true;
  };
  
  // aparecer dados na tela assim que a pagina for carregada
  onMounted(getProducts);
  </script>
  
  <style>
  .table-container {
    border: 1px solid #ccc;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
    overflow: hidden; /* Garante que o conteúdo da tabela respeite o border-radius */
  }
  </style>