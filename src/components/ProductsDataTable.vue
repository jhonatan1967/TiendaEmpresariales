<template>
    <div>
        <v-row>
            <v-col cols="4">
                <v-text-field v-model="item" type="text"></v-text-field>
                <v-btn type="button" v-on:click="busquedainicial">Buscar</v-btn>
                <v-btn type="button" v-on:click="atras">Atras</v-btn>
                <v-btn type="button" v-on:click="siguiente">Siguiente</v-btn>
            </v-col>
        </v-row>
        <v-row>
            <v-col>
                <v-data-table :headers="headers" :items="items[3]" :items-per-page="50" class="elevation-1"
                    disable-pagination>
                    <template v-slot:[`item.thumbnail`]="{ item }">
                        <div class="p-2">
                            <v-img :src="item.thumbnail" :alt="item.thumbnail" height="100px"></v-img>
                        </div>
                    </template>
                    <template v-slot:[`item.detalle`]="{ item }">
                        <v-icon class="ml-2" @click="detalle(item.id)">
                            mdi-eye
                        </v-icon>
                    </template>
                </v-data-table>
            </v-col>
        </v-row>
        <template>
            <v-row justify="center">
                <v-dialog v-model="dialog" persistent max-width="700">
                    <v-card>
                        <v-card-title class="headline">
                            {{ currentItem.title }}
                        </v-card-title>
                        <v-card-text>
                            Precio: {{ currentItem.price }}
                        </v-card-text>
                         <v-card-text>
                            Disponibles: {{ currentItem.available_quantity }}
                        </v-card-text>
                        <v-card-text>
                             <v-img :src="currentItem.thumbnail" :alt="currentItem.thumbnail" height="400"></v-img>
                        </v-card-text>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn color="green darken-1" text @click="dialog = false">
                                Cancelar
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </v-row>
        </template>
    </div>
</template>
<script>
    import axios from "axios";
    export default {
        name: "Products",
        data() {
            return {
                currentItem: null,
                dialog: false,
                headers: [

                    {
                        text: 'Nombre',
                        value: 'title'
                    },
                    {
                        text: 'Precio',
                        value: 'price'
                    },
                    {
                        text: 'Imagen',
                        value: 'thumbnail'
                    },
                    {
                        text: 'Ciudad',
                        value: 'address.state_name'
                    },
                    {
                        text: 'Ver',
                        value: 'detalle'
                    }
                ],
                item: "TV",
                items: [],
                limite: 50,
                offset: 0,
                total: 0
            }
        },
        methods: {
            async detalle(val) {
                if (val) {
                    await axios
                        .get("https://api.mercadolibre.com/items/" + val)
                        .then((response) => {
                            this.currentItem = response.data;
                        })
                }
                this.dialog = true
            },
            async busqueda() {
                if (this.item != "") {
                    await axios
                        .get("https://api.mercadolibre.com/sites/MCO/search?q=" + this.item + '&offset=' + this
                            .offset + '&limit=50')
                        .then((response) => {
                            this.items = Object.values(response.data)

                            this.total = this.items[2].total

                            console.log(this.total)
                        })
                }
            },
            async busquedainicial() {
                this.offset = 0
                this.busqueda()
            },

            async atras() {
                if (this.offset > 0) {
                    this.offset = this.offset - this.limite
                    this.busqueda()
                }
                console.log(this.offset)
            },

            async siguiente() {
                if (this.offset < this.total - this.limite) {
                    this.offset = this.offset + this.limite
                    this.busqueda()
                }
                console.log(this.offset)
            }
        },
        async mounted() {
             this.dialog = false
            await this.detalle("MCO613489353")
            await this.busqueda()
        }
    }
</script>