<template>
    <BaseForPagesTwoColumns>
        <v-row class="pa-0 ma-0" justify="space-between">
            <!-- PRODUCTS LIST -->
            <v-col cols="12" md="4" class="pa-2 ma-0 leftBaseColumn" align="start">
                <!-- MENU -->
                <v-row class="pa-0 ma-0 mb-4" justify="space-between">
                    <v-text-field
                        v-model="search"
                        placeholder="Pesquisar produto"
                        outlined
                        dense
                        append-icon="mdi-magnify"
                        class="personalized-text-field"
                        @input="filterProducts"
                    />
                    <v-spacer />

                    <v-menu offset-y :close-on-content-click="false" ref="filterMenu">
                        <template v-slot:activator="{ on, attrs }">
                            <v-btn
                                icon
                                color="blue-grey"
                                v-bind="attrs"
                                v-on="on"
                            >
                                <v-icon>mdi-filter-variant</v-icon>
                            </v-btn>
                        </template>
                        <v-card>
                            <v-card-title>Filtrar produtos</v-card-title>
                            <v-card-text style="max-height: 300px; overflow-y: auto;">
                                <v-row>
                                    <v-col cols="12">
                                        <v-divider></v-divider>
                                        <v-subheader>Tipo</v-subheader>
                                        <v-checkbox
                                            v-model="filterType"
                                            :value="'Material'"
                                            label="Material"
                                            dense
                                        />
                                        <v-checkbox
                                            v-model="filterType"
                                            :value="'Serviço'"
                                            label="Serviço"
                                            dense
                                        />
                                    </v-col>
                                </v-row>
                                <v-row>
                                    <v-col cols="12">
                                        <v-divider></v-divider>
                                        <v-subheader>Disponibilidade</v-subheader>
                                        <v-checkbox
                                            v-model="filterDisponibility"
                                            :value="'Disponível'"
                                            label="Disponível"
                                            dense
                                        />
                                        <v-checkbox
                                            v-model="filterDisponibility"
                                            :value="'Indisponível'"
                                            label="Indisponível"
                                            dense
                                        />
                                    </v-col>
                                </v-row>
                            </v-card-text>
                            <v-card-actions>
                                <v-spacer />
                                <v-btn color="blue-grey" outlined small @click="applyFilters">Aplicar</v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-menu>

                    <v-tooltip bottom>
                        <template v-slot:activator="{ on, attrs }">
                            <v-btn
                                icon
                                color="blue-grey"
                                v-bind="attrs"
                                v-on="on"
                                @click="openDialog"
                            >
                                <v-icon>mdi-note-plus</v-icon>
                            </v-btn>
                        </template>
                        <span>Adicionar produto</span>
                    </v-tooltip>
                </v-row>

                <!-- LIST -->
                <v-row class="pa-0 ma-0 custom-scroll-left-area">
                    <v-col
                        v-for="(product, index) in filteredProducts"
                        :key="`product-${index}-${Date.now()}-${Math.random().toString(36).substr(2, 9)}`"
                        cols="12"
                        class="pa-0 ma-0 pr-1"
                    >
                        <v-row class="pa-2 ma-0 mb-1 archiveList" justify="space-between" @click="selectProduct(product)">
                            <v-col cols="2" class="pa-0 ma-0">
                                <v-icon color="blue-grey">mdi-cube</v-icon>
                            </v-col>
                            <v-col cols="7" class="pa-0 ma-0">
                                <span>{{ product.name }}</span>
                            </v-col>
                            <v-col cols="3" class="pa-0 ma-0" align="end">
                                <v-chip
                                    class="ma-0"
                                    :color="product.isAvailable ? 'blue' : 'grey'"
                                    dark
                                    x-small
                                >
                                    {{ getProductStatus(product) }}
                                </v-chip>
                            </v-col>
                        </v-row>
                    </v-col>
                </v-row>
            </v-col>

            <!-- PRODUCT DATA -->
            <v-col cols="12" md="8" class="pa-0 ma-0 rightBaseColumn" align="center">
                <v-container class="ma-0 pa-0">
                    <h3 v-if="isProductSelected === false" class="ma-0 pa-0">Selecione um produto para visualizar.</h3>
                    <v-row v-else class="ma-0 pa-0 custom-scroll-area" justify="center">
                        <v-col cols="12" md="10" class="ma-0 pa-0 pr-1" align="start">
                            <v-container class="ma-0 pa-0">
                                <v-row class="ma-0 pa-0" justify="space-between">
                                    <v-col cols="12" class="ma-0 pa-0">
                                        <v-container fluid>
                                            <!-- DATA MENU -->
                                            <v-row class="ma-0 pa-0" justify="space-between">
                                                <v-btn
                                                    outlined
                                                    small
                                                    color="blue-grey"
                                                    class="mr-1"
                                                    @click="openDialogForEditing()"
                                                >
                                                    editar
                                                    <v-icon right dark>mdi-pencil</v-icon>
                                                </v-btn>

                                                <v-btn
                                                    outlined
                                                    small
                                                    color="#BD2A2E"
                                                    @click="deleteProduct(selectedProduct._id)"
                                                >
                                                    deletar
                                                    <v-icon right dark>mdi-delete</v-icon>
                                                </v-btn>

                                                <v-spacer />

                                                <v-tooltip bottom>
                                                    <template v-slot:activator="{ on, attrs }">
                                                        <v-btn
                                                            icon
                                                            color="#BD2A2E"
                                                            class="pa-0 ma-0"
                                                            v-bind="attrs"
                                                            v-on="on"
                                                            @click="isProductSelected = false"
                                                        >
                                                            <v-icon class="pa-0 ma-0">mdi-close</v-icon>
                                                        </v-btn>
                                                    </template>
                                                    <span>Fechar</span>
                                                </v-tooltip>
                                            </v-row>

                                            <v-divider inherit class="mb-2" />

                                            <!-- DATA DASHBOARD -->
                                            <v-row>
                                                <v-col cols="12">
                                                    <v-banner elevation="3" outlined shaped class="mb-4">
                                                        <template v-slot:icon>
                                                            <v-avatar color="blue-grey">
                                                                <v-icon dark>mdi-cube</v-icon>
                                                            </v-avatar>
                                                        </template>
                                                        <div class="ml-3">
                                                            <div class="caption">Produto</div>
                                                            <h2 class="text-no-wrap mt-3 pb-1">{{ selectedProduct.name }}</h2>
                                                        </div>
                                                    </v-banner>
                                                </v-col>
                                            </v-row>

                                            <v-row>
                                                <v-col cols="12" md="6">
                                                    <v-banner elevation="3" outlined shaped class="mb-4">
                                                        <template v-slot:icon>
                                                            <v-avatar color="blue-grey">
                                                                <v-icon dark>mdi-currency-usd</v-icon>
                                                            </v-avatar>
                                                        </template>
                                                        <div class="ml-3">
                                                            <div class="caption">Preço de Custo</div>
                                                            <h2 class="text-no-wrap mt-3 pb-1">R$ {{ Number(selectedProduct.costPrice).toFixed(2) }}</h2>
                                                        </div>
                                                    </v-banner>
                                                </v-col>

                                                <v-col cols="12" md="6">
                                                    <v-banner elevation="3" outlined shaped class="mb-4">
                                                        <template v-slot:icon>
                                                            <v-avatar color="blue-grey">
                                                                <v-icon dark>mdi-currency-usd</v-icon>
                                                            </v-avatar>
                                                        </template>
                                                        <div class="ml-3">
                                                            <div class="caption">Preço de Venda</div>
                                                            <h2 class="text-no-wrap mt-3 pb-1">R$ {{ Number(selectedProduct.price).toFixed(2) }}</h2>
                                                        </div>
                                                    </v-banner>
                                                </v-col>
                                            </v-row>

                                            <v-row>
                                                <v-col cols="12" md="6">
                                                    <v-banner elevation="3" outlined shaped class="mb-4">
                                                        <template v-slot:icon>
                                                            <v-avatar color="blue-grey">
                                                                <v-icon dark>mdi-tag</v-icon>
                                                            </v-avatar>
                                                        </template>
                                                        <div class="ml-3">
                                                            <div class="caption">Tipo</div>
                                                            <h2 class="text-no-wrap mt-3 pb-1">{{ selectedProduct.type }}</h2>
                                                        </div>
                                                    </v-banner>
                                                </v-col>

                                                <v-col cols="12" md="6" v-if="selectedProduct.type !== 'Serviço'">
                                                    <v-banner elevation="3" outlined shaped class="mb-4">
                                                        <template v-slot:icon>
                                                            <v-avatar color="blue-grey">
                                                                <v-icon dark>mdi-format-list-numbered</v-icon>
                                                            </v-avatar>
                                                        </template>
                                                        <div class="ml-3">
                                                            <div class="caption">Estoque</div>
                                                            <h2 class="text-no-wrap mt-3 pb-1">{{ selectedProduct.supply }}</h2>
                                                        </div>
                                                    </v-banner>
                                                </v-col>
                                            </v-row>
                                        </v-container>
                                    </v-col>
                                </v-row>
                            </v-container>
                        </v-col>
                    </v-row>
                </v-container>
            </v-col>

            <!-- DIALOG PARA CRIAR/EDITAR PRODUTO -->
            <template>
                <v-row justify="center">
                    <v-dialog
                        v-model="dialog"
                        persistent
                        max-width="900px"
                    >
                        <v-card>
                            <v-card-title>
                                <span class="text-h5">{{ newOrEditBtn }} Produto</span>
                            </v-card-title>
                            <v-card-text>
                                <v-col cols="12">
                                    <v-row>
                                        <v-col cols="12">
                                            <v-text-field
                                                v-model="newProduct.name"
                                                label="Nome"
                                                outlined
                                                dense
                                                class="personalized-text-field"
                                            />
                                        </v-col>

                                        <v-col cols="12" md="6">
                                            <v-text-field
                                                v-model="newProduct.costPrice"
                                                @keypress="validateInput($event)"
                                                label="Preço de Custo"
                                                outlined
                                                dense
                                                prefix="R$"
                                                class="personalized-text-field"
                                            />
                                        </v-col>

                                        <v-col cols="12" md="6">
                                            <v-text-field
                                                v-model="newProduct.price"
                                                @keypress="validateInput($event)"
                                                label="Preço"
                                                outlined
                                                dense
                                                prefix="R$"
                                                class="personalized-text-field"
                                            />
                                        </v-col>

                                        <v-col cols="12" md="6">
                                            <v-select
                                                v-model="newProduct.type"
                                                :items="['Serviço', 'Material']"
                                                label="Tipo"
                                                outlined
                                                dense
                                                class="personalized-text-field"
                                            />
                                        </v-col>

                                        <v-col cols="12" md="6" v-if="newProduct.type !== 'Serviço'">
                                            <v-text-field
                                                v-model="newProduct.supply"
                                                type="number"
                                                label="Estoque"
                                                outlined
                                                dense
                                                class="personalized-text-field"
                                            />
                                        </v-col>
                                    </v-row>
                                </v-col>
                            </v-card-text>

                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn
                                    color="error darken-1"
                                    text
                                    @click="closeDialog()"
                                >
                                    Cancelar
                                </v-btn>
                                <v-btn
                                    color="green darken-1"
                                    text
                                    @click="saveProduct()"
                                >
                                    Salvar
                                </v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-dialog>
                </v-row>
            </template>

            <Snackbar :show.sync="snackbar.show" :message="snackbar.message" :color="snackbar.color" />
        </v-row>
    </BaseForPagesTwoColumns>
</template>

<script>
// Import necessary components and libraries
import BaseForPagesTwoColumns from '@/components/BaseForPagesTwoColumns.vue';
import axios from 'axios';
import { mapGetters } from 'vuex';
import Snackbar from '@/components/Snackbar.vue';

export default {
    components: {
        BaseForPagesTwoColumns, // Layout component for a two-column structure
        Snackbar,               // Snackbar component for notifications
    },

    // Lifecycle hook to trigger data fetch when component is mounted
    async mounted() {
        this.$fetch();
    },

    // Fetches products data for the user based on their company ID
    async fetch() {
        if (!this.user || !this.user.companyId) {
            console.error('User data is not available or companyId is missing.');
            return;
        }

        console.log('User companyId:', this.user.companyId);
        try {
            const response = await axios.get('http://localhost:2727/products', {
                params: { companyId: this.user.companyId },
                headers: {
                    Authorization: `Bearer ${this.token}`, // Authorization header with user token
                },
                withCredentials: true // Ensures cross-site Access-Control requests include credentials
            });
            this.products = response.data; // Assign fetched data to products array
            console.log('Products:', this.products);
        } catch (error) {
            console.error('Error fetching products:', error);
        }
    },

    data: () => ({
        isProductSelected: false,  // Tracks if a product is selected
        selectedProduct: null,     // Currently selected product
        products: [],              // All products fetched
        filteredProducts: [],      // Products matching search and filter criteria
        dialog: false,             // Toggles visibility of the product dialog
        search: '',                // Search query for filtering products
        categories: ["Básicos", "Impostos", "Insumos", "Transportes", "Salários", "Serviços"], // Predefined categories
        newProduct: {              // Object to store details for a new or edited product
            name: null,
            price: null,
            costPrice: null,
            supply: null,
            type: null,
            isAvailable: null,
        },
        newOrEditBtn: 'Novo',      // Button label indicating add/edit state
        isEditing: false,          // Flag for edit mode
        filterType: [],            // Selected types for filtering
        filterDisponibility: [],   // Selected availability status for filtering
        snackbar: {                // Snackbar notification data
            show: false,
            message: '',
            color: 'success',
        },
    }),

    watch: {
        products: 'filterProducts', // Triggers product filtering when products list changes
        search: 'filterProducts'    // Triggers product filtering when search query changes
    },

    computed: {
        ...mapGetters({
            user: 'auth/user',     // Accesses user data from Vuex
            token: 'auth/token',   // Accesses auth token from Vuex
        }),
    },

    methods: {
        // Selects a product and sets it as the currently selected item
        selectProduct(product) {
            this.isProductSelected = true;
            this.selectedProduct = product;
        },

        // Opens the dialog for creating a new product
        openDialog() {
            this.resetForm();
            this.newOrEditBtn = 'Novo';
            this.dialog = true;
        },

        // Opens the dialog for editing the selected product
        openDialogForEditing() {
            this.isEditing = true;
            this.newOrEditBtn = 'Editar';
            this.newProduct = {
                name: this.selectedProduct.name,
                price: this.selectedProduct.price,
                costPrice: this.selectedProduct.costPrice,
                supply: this.selectedProduct.supply,
                type: this.selectedProduct.type,
                isAvailable: this.selectedProduct.isAvailable,
            };
            this.dialog = true;
        },

        // Closes the product dialog and resets the form
        closeDialog() {
            this.resetForm();
            this.dialog = false;
        },

        // Resets the new product form fields and state
        resetForm() {
            this.newProduct = {
                name: null,
                price: null,
                costPrice: null,
                supply: null,
                type: null,
                isAvailable: null,
            };
            this.newOrEditBtn = 'Novo';
            this.isEditing = false;
        },

        // Saves product data, either adding or updating depending on edit state
        saveProduct() {
            if (!this.newProduct.name || !this.newProduct.price || !this.newProduct.costPrice || (this.newProduct.type !== 'Serviço' && !this.newProduct.supply) || !this.newProduct.type) {
                alert('Por favor, preencha todos os campos obrigatórios.');
                return;
            }

            if (this.isEditing) {
                this.updateProduct();
            } else {
                this.addProduct();
            }

            this.resetForm();
            this.dialog = false;
        },

        // Adds a new product to the database and updates the products list
        async addProduct() {
            try {
                const newProduct = { ...this.newProduct };
                if (newProduct.type === 'Serviço') {
                    newProduct.isAvailable = true;
                } else if (newProduct.type === 'Material') {
                    newProduct.isAvailable = newProduct.supply > 0;
                }

                const newProductWithCompany = { ...newProduct, companyId: this.user.companyId };
                const response = await axios.post('http://localhost:2727/products', newProductWithCompany, {
                    headers: { Authorization: `Bearer ${this.token}` },
                    withCredentials: true,
                });
                this.products.push(response.data);
                this.filteredProducts.push(response.data);
                this.$fetch();
                this.showSnackbar('Produto adicionado com sucesso!', 'blue-grey');
            } catch (error) {
                console.error('Error adding product:', error);
                this.showSnackbar(`Erro ao adicionar produto: ${error.response ? error.response.data.message : error.message}`, 'error');
            }
        },

        // Updates the selected product with new data
        async updateProduct() {
            try {
                const updatedProductWithCompany = { ...this.newProduct, companyId: this.user.companyId };
                if (updatedProductWithCompany.type === 'Material') {
                    updatedProductWithCompany.isAvailable = updatedProductWithCompany.supply > 0;
                }

                const response = await axios.put(`http://localhost:2727/products/${this.selectedProduct._id}`, updatedProductWithCompany, {
                    headers: { Authorization: `Bearer ${this.token}` },
                    withCredentials: true,
                });
                const index = this.products.findIndex(product => product._id === this.selectedProduct._id);
                if (index !== -1) {
                    this.products.splice(index, 1, response.data);
                    this.filteredProducts.splice(index, 1, response.data);
                    this.selectedProduct = response.data;
                }
                this.showSnackbar('Produto atualizado com sucesso!', 'blue-grey');
                this.$fetch();
            } catch (error) {
                console.error('Error updating product:', error);
                this.showSnackbar(`Erro ao atualizar produto: ${error.response ? error.response.data.message : error.message}`, 'error');
            }
        },

        // Deletes a product from the database and updates the product list
        async deleteProduct(productId) {
            try {
                await axios.delete(`http://localhost:2727/products/${productId}`, {
                    headers: { Authorization: `Bearer ${this.token}` },
                    withCredentials: true,
                });
                this.products = this.products.filter(product => product._id !== productId);
                this.filteredProducts = this.filteredProducts.filter(product => product._id !== productId);
                this.selectedProduct = null;
                this.isProductSelected = false;
                this.showSnackbar('Produto deletado!', 'blue-grey');
            } catch (error) {
                console.error('Error deleting product:', error);
                this.showSnackbar(`Erro ao deletar produto: ${error.response ? error.response.data.message : error.message}`, 'error');
            }
        },

        // Filters products based on search query
        filterProducts(value) {
            if (typeof value !== 'string') {
                value = '';
            }
            this.filteredProducts = this.products.filter(product => {
                return product.name && product.name.toLowerCase().includes(value.toLowerCase());
            });
        },

        // Applies selected type and availability filters to the product list
        applyFilters() {
            this.filteredProducts = this.products.filter(product => {
                const typeMatch = this.filterType.length ? this.filterType.includes(product.type) : true;
                const availabilityMatch = this.filterDisponibility.length
                    ? this.filterDisponibility.includes(product.isAvailable ? 'Disponível' : 'Indisponível')
                    : true;

                return typeMatch && availabilityMatch;
            });

            // Close the filter menu
            this.$refs.filterMenu.isActive = false;
        },

        // Validates numeric input for fields
        validateInput(event) {
            const char = String.fromCharCode(event.keyCode);
            if (!/[0-9+.]/.test(char)) {
                event.preventDefault();
            }
        },

        // Returns availability status of the product
        getProductStatus(product) {
            if (product.isAvailable === true) {
                return 'Disponível';
            } else if (product.isAvailable === false) {
                return 'Indisponível';
            }
        },

        // Shows a snackbar notification with a message and color
        showSnackbar(message, color = 'success') {
            this.snackbar.message = message;
            this.snackbar.color = color;
            this.snackbar.show = true;
        }
    }
};
</script>

<style scoped>
.leftBaseColumn {
    height: calc(100vh - 30px - 30px - 85px - 100px);
    border: 1px solid #B4BEC9;
    border-radius: 1%;
}

.rightBaseColumn {
    height: calc(100vh - 30px - 30px - 85px - 100px);
}

.archiveList {
    border: 1px solid #002333;
    border-radius: 1%;
    height: 40px;
    transition: all 0.3s ease;
}

.archiveList:hover {
    background-color: #b4bec9ab; /* Cor de fundo ao passar o mouse */
    border-color: black; /* Cor da borda ao passar o mouse */
}

/* Customiza a barra de rolagem */
.custom-scroll-area {
    max-height: calc(100vh - 30px - 30px - 85px - 100px);; /* Altura máxima definida */
    overflow-y: auto; /* Habilita o scroll vertical */
}

.custom-scroll-area::-webkit-scrollbar {
    width: 10px; /* Largura da barra de rolagem */
}

.custom-scroll-area::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, 0.3); /* Cor da barra de rolagem */
    border-radius: 5px; /* Bordas arredondadas */
}

.custom-scroll-area::-webkit-scrollbar-track {
    background: rgba(0, 0, 0, 0.1); /* Cor da trilha da barra de rolagem */
}

/* Customiza a barra de rolagem */
.custom-scroll-left-area {
    max-height: calc(100vh - 30px - 30px - 85px - 170px);; /* Altura máxima definida */
    overflow-y: auto; /* Habilita o scroll vertical */
}

.custom-scroll-left-area::-webkit-scrollbar {
    width: 10px; /* Largura da barra de rolagem */
}

.custom-scroll-left-area::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, 0.3); /* Cor da barra de rolagem */
    border-radius: 5px; /* Bordas arredondadas */
}

.custom-scroll-left-area::-webkit-scrollbar-track {
    background: rgba(0, 0, 0, 0.1); /* Cor da trilha da barra de rolagem */
}

.personalized-text-field {
    height: 40px;
}
</style>
