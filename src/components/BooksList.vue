<template>
<div>
    <b-navbar toggleable="lg" type="dark" variant="primary">
        <b-navbar-brand href="#">Bíblia Ave-Maria</b-navbar-brand>

        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <!-- <b-collapse id="nav-collapse" is-nav>
            <b-navbar-nav class="ml-auto">
                <b-nav-form>
                    <b-form-input size="md" class="mr-sm-2 rounded-0" placeholder="Procurar"></b-form-input>
                    <b-button squared size="md" type="submit" variant="outline-light">Procurar</b-button>
                </b-nav-form>
            </b-navbar-nav>
        </b-collapse> -->
    </b-navbar>

    <b-container class="mx-auto">
        <div v-if="livros && !capitulos && !texto" class="text-left">
            <br/>
            <b-row class="books-list">
                <b-col>
                    <h4>Antigo Testamento</h4>
                    <b-row>
                        <b-col cols="6" v-for="(nomeLivro, index) in livros.AT" :key="nomeLivro">
                            <b-button squared
                                variant="link" 
                                class="button-book text-left" 
                                size="lg"
                                @click="abrirLivro('AT', index, nomeLivro)"
                            >
                                {{nomeLivro}}
                            </b-button>
                        </b-col>
                    </b-row>
                </b-col>
                <b-col>
                    <h4>Novo Testamento</h4>
                    <b-row>
                        <b-col cols="6" v-for="(nomeLivro, index) in livros.NT" :key="nomeLivro" class="text-left">
                            <b-button squared
                                variant="link" 
                                class="button-book text-left" 
                                size="lg"
                                @click="abrirLivro('NT', index, nomeLivro)"
                            >
                                {{nomeLivro}}
                            </b-button>
                        </b-col>
                    </b-row>
                </b-col>
            </b-row>
        </div>

        <div v-if="livros && capitulos && !texto" class="text-left">
            <br/>
            <b-button squared variant="primary" class="back-button" @click="voltarParaLivros()">
                <i class="fas fa-long-arrow-alt-left"></i>
            </b-button> 
            <h3>
                {{livroSelecionado.slice(3)}}
            </h3>
            <b-row>
                <div v-for="capitulo in capitulos" :key="capitulo" >
                    <b-button squared
                        variant="link" 
                        class="button-chap" 
                        size="lg"
                        @click="abrirCapitulo(capitulo)"
                    >
                        {{capitulo}}
                    </b-button>
                </div>
            </b-row>
        </div>

        <div v-if="livros && capitulos && texto" class="text-left">
            <br/>
            <b-button squared variant="primary" class="back-button"  @click="voltarParaCapitulos()">
                <i class="fas fa-long-arrow-alt-left"></i>
            </b-button> 
            <h3>
                {{livroSelecionado.slice(3)}}, {{capituloSelecionado}}
            </h3>
            
            <b-row>
                <b-col cols="8">
                    <p class="texto" v-for="(versiculo, index) in texto" :key="index">
                        {{versiculo}}
                    </p>
                </b-col>
                <b-col>
                    <b-card title="Capítulos">
                    <b-row>
                        <div v-for="capitulo in capitulos" :key="capitulo" >
                            <b-button squared
                                variant="link" 
                                class="button-book" 
                                size="lg"
                                @click="abrirCapitulo(capitulo)"
                            >
                                {{capitulo}}
                            </b-button>
                        </div>
                    </b-row>
                    </b-card>
                </b-col>
            </b-row>
        </div>

    </b-container>


</div>
</template>

<style>
.books-list {
    font-size: 10px;
}

.button-book {
    margin: -10px;
}

.button-chap {
    width: 80px;
    height: 50px;
}

.back-button {
    margin: 10px 0px 10px 0px;
}

.texto {
    font-size: 20px;
}
</style>

<script>

const axios = require('axios');
import {URL} from '../consts/service-url';

export default {
    name: 'BooksList',
    mounted: async function() { 
        try{
            let res = await axios.get(URL.REMOTE + 'livros');
            this.livros = res.data;
        } catch (e) {
            console.error(e);
        }   
    },

    methods: {
        abrirLivro: async function(testamento, index, nomeLivro) {
            
            this.testamentoSelecionado = testamento;
            
            // computar nome do livro com seu número identificador
            let idLivro = undefined;
            
            if(testamento == 'NT') index += this.livros.AT.length + 1;
            else index += 1;
            
            if(index < 10) {
                idLivro = `0${index} ${nomeLivro}`;
            } else {
                idLivro = `${index} ${nomeLivro}`;
            }

            this.livroSelecionado = idLivro;

            let requestConfig = {
                method: 'post',
                url: URL.REMOTE + 'capitulos',
                headers: {
                    'Content-Type': 'application/json'
                },
                data: {
                    testamento: testamento,
                    livro: idLivro
                }
            };

            let res = await axios(requestConfig);
            this.capitulos = res.data;
        },

        abrirCapitulo: async function(capitulo) {

            this.capituloSelecionado = parseInt(capitulo);

            if(this.capituloSelecionado < 10) {
                capitulo = `0${this.capituloSelecionado}`;
            }

            let requestConfig = {
                method: 'post',
                url: URL.REMOTE + 'texto',
                headers: {
                    'Content-Type': 'application/json'
                },
                data: {
                    testamento: this.testamentoSelecionado,
                    livro: this.livroSelecionado,
                    capitulo: capitulo
                }
            };

            let res = await axios(requestConfig);
            this.texto = res.data.slice(1);
            console.log(this.texto);
        },

        voltarParaLivros: function() {
            this.capitulos = undefined;
            this.livroSelecionado = undefined;
            this.testamentoSelecionado = undefined;
            this.texto = undefined;
        },

        voltarParaCapitulos: function() {
            this.texto = undefined;
            this.capituloSelecionado = undefined;
        }
    },

    data() {
        return {
            livros: undefined,
            livroSelecionado: undefined,
            testamentoSelecionado: undefined,
            capitulos: undefined,
            capituloSelecionado: undefined,
            texto: undefined
        }
    }


}
</script>
