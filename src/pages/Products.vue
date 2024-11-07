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
                                <th>
                                    Nome
                                </th>
                                <th>
                                    Preço
                                </th>
                                <th>
                                    Ações
                                </th>
                            </tr>
                        </thead>

                        <tbody>
                            <!-- criando for para listar os produtos -->
                            <tr v-for="(product, index) in products" key="product.id">
                                <td>{{ product.name }}</td>
                                <td>{{ product.price }}</td>

                                <!-- criando botões de delete e edit -->
                                <td>
                                    <v-btn icon>
                                        <v-icon @click="editProduct(product)">mdi-pencil</v-icon>
                                    </v-btn>
                                    <v-btn icon>
                                        <v-icon @click="deleteProducts(product.id)">mdi-delete</v-icon>
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
                <!-- criando titulo do card -->
                <v-card-title>
                    {{ isEdit ? 'Editar Produto' : 'Cadastrar Produto' }}
                </v-card-title>
                <!-- criando inputs de texto para o modal -->
                <v-card-text>
                    <v-form>
                        <v-text-field label="Nome do produto" v-model="productName"></v-text-field>
                        <v-text-field label="Preço do produto" v-model="productPrice"></v-text-field>
                    </v-form>
                </v-card-text>
                <!-- criando botoes de ação do modal -->
                <v-card-actions>
                    <v-btn color="blue darken-1" @click="dialog = false"> Cancelar</v-btn>
                    <v-btn color="blue darken-1" text @click="saveProduct">{{ isEdit ? 'Salvar' : 'Adicionar' }}</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-container>



</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import { da } from "vuetify/locale";
import { a } from "node_modules/unplugin-vue-router/dist/types-DBiN4-4c";

// definindo os tipos das variáveis
const dialog = ref(false);
const products = ref([]);
const productName = ref("");
const productPrice = ref("");

// funçao para listagem dos produtos
const getProducts = async () => {
    try {
        const response = await axios.get("http://localhost:3000/products");
        products.value = response.data;
    } catch (error) {
        console.error('Erro ao buscar produtos', error);
    }

};

// funçao para deleçao dos produtos
const deleteProducts = async (id) => {
    try {
        await axios.delete(`http://localhost:3000/products/${id}`);
        getProducts();
    } catch (error) {
        console.error('Erro ao deletar produto', error);
    }
};

// funçao para salvar e editar os produtos
const saveProduct = async () => {
    try {
        if (editId.value) {
            await axios.put(`https://localhost:3000/products/${editId.value}`, {
                name: productName.value,
                price: productPrice.value
            });
        } else {
            await axios.post(`https://localhost:3000/products`, {
                name: productName.value,
                price: productPrice.value
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
}

// funçao para abrir o modal de edit com os dados do produto 
const editProduct = (product) => {
    isEdit.value = true;
    productName.value = product.name;
    productPrice.value = product.price;
    editId.value = product.id;
    dialog.value = true;
}

// funçao para abrir o modal de cadastro
const openDialog = () => {
    isEdit.value = false;
    productName.value = '';
    productPrice.value = '';
    dialog.value = true;
};

// aparecer dados na tela assim que a pagina for carregada
onMounted(getProducts)
</script>

<style>
.table-container {
    border: 1px solid #ccc;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
    overflow: hidden;
    /* Garante que o conteúdo da tabela respeite o border-radius */
}
</style>