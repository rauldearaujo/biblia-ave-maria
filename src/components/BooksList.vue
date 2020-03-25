<template>
<div>
    <b-navbar toggleable="lg" type="dark" variant="dark">
        <b-navbar-brand href="#"><i class="fas fa-bible"></i> Bíblia Ave-Maria</b-navbar-brand>

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
                        <b-col lg="6" md="12" v-for="(nomeLivro, index) in livros.AT" :key="nomeLivro">
                            <b-button squared
                                variant="link" 
                                class="button-book text-left text-dark" 
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
                        <b-col lg="6" md="12" v-for="(nomeLivro, index) in livros.NT" :key="nomeLivro" class="text-left">
                            <b-button squared
                                variant="link" 
                                class="button-book text-left text-dark" 
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
            <b-button 
                squared variant="dark" 
                class="back-button"
                @click="voltarParaLivros()">
                    <i class="fas fa-long-arrow-alt-left"></i> Voltar
            </b-button> 
            <h3>
                {{livroSelecionado.slice(3)}}
            </h3>
            <b-row>
                <div v-for="capitulo in capitulos" :key="capitulo" >
                    <b-button squared
                        variant="link" 
                        class="button-chap text-dark" 
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
            <b-button squared 
                variant="dark" 
                class="back-button"
                @click="voltarParaCapitulos()">
                    <i class="fas fa-long-arrow-alt-left"></i> Voltar
            </b-button> 
            
            <b-row>
                <b-col>
                    <b-card title="Capítulos">
                    <b-row>
                        <div v-for="capitulo in capitulos" :key="capitulo" >
                            <b-button v-if="capitulo == capituloSelecionado" squared
                                variant="dark" 
                                size="lg"
                                @click="abrirCapitulo(capitulo)"
                            >
                                {{capitulo}}
                            </b-button>
                            <b-button v-else squared
                                variant="link"
                                class="text-dark"
                                size="lg"
                                @click="abrirCapitulo(capitulo)"
                            >
                                {{capitulo}}
                            </b-button>
                        </div>
                    </b-row>
                    </b-card>
                </b-col>
                <b-col lg="8" md="12" class="order-first">
                    <b-row>
                        <b-col>
                            <h3>
                                {{livroSelecionado.slice(3)}}, {{formatChapterNumber(capituloSelecionado)}}
                            </h3>
                        </b-col>
                        <b-col>
                            <b-button-group style="float:right">
                                <b-button squared 
                                    variant="dark"
                                    :disabled="capituloSelecionado === '01'"
                                    @click="abrirCapitulo(parseInt(capituloSelecionado) - 1)">
                                        <i class="fas fa-arrow-left"></i> Anterior
                                </b-button>
                                <b-button squared
                                    variant="dark"
                                    :disabled="capituloSelecionado === capitulos[capitulos.length-1]"
                                    @click="abrirCapitulo(parseInt(capituloSelecionado) + 1)">
                                        Próximo <i class="fas fa-arrow-right"></i>
                                </b-button>
                            </b-button-group>
                        </b-col>
                    </b-row>
                    <div v-if="loadingTexto">
                        <br/>
                        <vue-content-loading v-bind="$attrs" dark="#e0e0e0" secondary="#d0d0d0" :width="300" :height="120">
                            <rect x="0" y="0" rx="3" ry="3" width="250" height="10" />
                            <rect x="20" y="20" rx="3" ry="3" width="220" height="10" />
                            <rect x="20" y="40" rx="3" ry="3" width="170" height="10" />
                            <rect x="0" y="60" rx="3" ry="3" width="250" height="10" />
                            <rect x="20" y="80" rx="3" ry="3" width="200" height="10" />
                            <rect x="20" y="100" rx="3" ry="3" width="80" height="10" />
                        </vue-content-loading>
                    </div>
                    <div v-else>
                        <p class="texto" v-for="(versiculo, index) in texto" :key="index">
                            {{versiculo}}
                        </p>
                        <br/>
                        <b-button squared 
                                    variant="dark"
                                    :disabled="capituloSelecionado === '01'"
                                    @click="toTop()">
                                        <i class="fas fa-arrow-up"></i> Topo
                                </b-button>
                        <b-button-group style="float:right">
                                <b-button squared 
                                    variant="dark"
                                    :disabled="capituloSelecionado === '01'"
                                    @click="abrirCapitulo(parseInt(capituloSelecionado) - 1)">
                                        <i class="fas fa-arrow-left"></i> Anterior
                                </b-button>
                                <b-button squared
                                    variant="dark"
                                    :disabled="capituloSelecionado === capitulos[capitulos.length-1]"
                                    @click="abrirCapitulo(parseInt(capituloSelecionado) + 1)">
                                        Próximo <i class="fas fa-arrow-right"></i>
                                </b-button>
                            </b-button-group>
                    </div>
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

.selected-button {
    background-color: black;
}
</style>

<script>

const axios = require('axios');
import {URL} from '../consts/service-url';
import VueContentLoading from 'vue-content-loading';
import JQuery from 'jquery'

export default {
    name: 'BooksList',
    
    components: {
        VueContentLoading
    },

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
            this.loadingTexto = true;
            
            if(typeof capitulo === 'number') {
                this.capituloSelecionado = this.chapterNumber2String(capitulo);
            } else {
                this.capituloSelecionado = capitulo
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
                    capitulo: this.capituloSelecionado
                }
            };

            let res = await axios(requestConfig);
            this.texto = res.data.slice(1);
            this.loadingTexto = false;
        },

        chapterNumber2String: function(chapterInt) {
            if(this.base === 10) {
                if(chapterInt < 10) {
                    return `0${chapterInt}`;
                }
            }
            
            else if(this.base === 100) {
                if(chapterInt < 10) {
                    return `00${chapterInt}`;
                }
                else if(chapterInt >= 10 && chapterInt < 100) {
                    return `0${chapterInt}`;
                }
            }

            return `${chapterInt}`;
        },

        formatChapterNumber: function(chapterStr) {
            if(this.base === 10) {
                if(parseInt(chapterStr) < 10) {
                    return chapterStr.slice(1);
                }
            } 
            else if(this.base === 100) {
                if(parseInt(chapterStr) < 10) {
                    return chapterStr.slice(2);
                }
                else if(parseInt(chapterStr) >= 10 && parseInt(chapterStr) < 100) {
                    return chapterStr.slice(1);
                }
            }
            
            return chapterStr;
        },

        toTop: function() {
            JQuery('html, body').animate({
                scrollTop: 0
            }, 500, 'linear');
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

    computed: {
        base: function() {
            return this.livroSelecionado === '21 Salmos' ? 100 : 10;
        }
    },

    data() {
        return {
            livros: undefined,
            livroSelecionado: undefined,
            testamentoSelecionado: undefined,
            capitulos: undefined,
            capituloSelecionado: undefined,
            texto: undefined,
            loadingTexto: false
        }
    }


}
</script>
