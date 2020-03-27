<template>
<div>
    <b-navbar fixed="top" sticky  toggleable="lg" type="dark" variant="dark">
        <b-navbar-brand href="#" @click="voltarParaLivros()"><i class="fas fa-bible"></i> Bíblia Ave-Maria</b-navbar-brand>
        <b-button class="ml-auto" v-if="existemVersiculosSelecionados()" @click="copiarTexto()" squared size="md" type="submit" variant="warning">
            <i :class="btCopiarIcone"></i><span class="d-none d-sm-block d-sm-none d-md-block float-right ml-2">{{btCopiarTexto}}</span>
        </b-button>
        <!-- <b-collapse id="nav-collapse" is-nav>
            <b-navbar-nav class="ml-auto">
                <b-nav-form>
                    <b-form-input size="md" class="mr-sm-2 rounded-0" placeholder="Procurar"></b-form-input>
                    <b-button squared size="md" type="submit" variant="outline-light">Procurar</b-button>
                </b-nav-form>
            </b-navbar-nav>
        </b-collapse> -->
    </b-navbar>

    <b-container>
        
        <div v-if="livros && !capitulos && !versiculos" class="text-left books-list-background">
            
            <div v-if="livroSelecionado">
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

            <b-row v-else>
                <b-col class="books-list">
                    <h4>Antigo Testamento</h4>
                    <ul class="list-unstyled book-columns">
                        <li v-for="(nomeLivro, index) in livros.AT" :key="nomeLivro">
                            <b-button squared
                                variant="link" 
                                class="button-book text-left text-dark" 
                                size="lg"
                                @click="abrirLivro('AT', index, nomeLivro)"
                            >
                                {{nomeLivro}}
                            </b-button>
                        </li>
                    </ul>
                </b-col>
                <b-col class="books-list">
                    <h4>Novo Testamento</h4>
                    <ul class="list-unstyled book-columns">
                        <li v-for="(nomeLivro, index) in livros.NT" :key="nomeLivro">
                            <b-button squared
                                variant="link" 
                                class="button-book text-left text-dark" 
                                size="lg"
                                @click="abrirLivro('NT', index, nomeLivro)"
                            >
                                {{nomeLivro}}
                            </b-button>
                        </li>
                    </ul>
                </b-col>
            </b-row>
        </div>
        
        <div v-if="!livros">
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

        <div v-if="livros && capitulos && !versiculos" class="text-left">
            
            <div v-if="capituloSelecionado">
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
                <br/>
                <b-button 
                    squared variant="dark" 
                    class="back-button"
                    @click="voltarParaLivros()">
                        <i class="fas fa-long-arrow-alt-left"></i><span class="d-none d-sm-block d-sm-none d-md-block float-right ml-2">Voltar</span>
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
        </div>

        <div v-if="livros && capitulos && versiculos" class="text-left">
            <br/>
            <b-button squared 
                variant="dark" 
                class="back-button"
                @click="voltarParaCapitulos()">
                    <i class="fas fa-long-arrow-alt-left"></i><span class="d-none d-sm-block d-sm-none d-md-block float-right ml-2">Voltar</span>
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
                                        <i class="fas fa-arrow-left"></i> <span class="d-none d-sm-block d-sm-none d-md-block float-right ml-2">Anterior</span>
                                </b-button>
                                <b-button squared
                                    variant="dark"
                                    :disabled="capituloSelecionado === capitulos[capitulos.length-1]"
                                    @click="abrirCapitulo(parseInt(capituloSelecionado) + 1)">
                                    <span class="d-none d-sm-block d-sm-none d-md-block float-left mr-2">Próximo</span> <i class="fas fa-arrow-right"></i>
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
                        
                        <div @click="toggleSelecionarVersiculo(index)" class="texto" v-for="(versiculo, index) in versiculos" :key="index">
                            <p :class="[versiculosSelecionados[index] ? 'versiculo-selecionado versiculo' : 'versiculo']">{{versiculo}}</p>
                        </div>
                        <br/>
                        <b-button squared 
                                    variant="dark"
                                    :disabled="capituloSelecionado === '01'"
                                    @click="toTop()">
                                        <i class="fas fa-arrow-up"></i> <span class="d-none d-sm-block d-sm-none d-md-block float-right ml-2">Topo</span>
                                </b-button>
                        <b-button-group style="float:right">
                            <b-button squared 
                                variant="dark"
                                :disabled="capituloSelecionado === '01'"
                                @click="abrirCapitulo(parseInt(capituloSelecionado) - 1)">
                                    <i class="fas fa-arrow-left"></i> <span class="d-none d-sm-block d-sm-none d-md-block float-right ml-2">Anterior</span>
                            </b-button>
                            <b-button squared
                                variant="dark"
                                :disabled="capituloSelecionado === capitulos[capitulos.length-1]"
                                @click="abrirCapitulo(parseInt(capituloSelecionado) + 1)">
                                <span class="d-none d-sm-block d-sm-none d-md-block float-left mr-2">Próximo</span> <i class="fas fa-arrow-right"></i>
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
    margin-top: 20px;
    font-size: 10px;
}

.book-columns {
    column-count: 2;
}

.button-book {
    margin: -7px;
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

.versiculo {
    margin: 0px;
    cursor: pointer;
}

.versiculo-selecionado {
    background-color: var(--warning);
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
            this.loadingCapitulos = true;
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
            this.loadingCapitulos = false;
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
            this.versiculos = res.data.slice(1);
            this.versiculosSelecionados = this.versiculos.map(() => {return false});
            this.loadingTexto = false;
        },

        toggleSelecionarVersiculo: function(indiceVersiculo) {
            this.$set(this.versiculosSelecionados, indiceVersiculo, !this.versiculosSelecionados[indiceVersiculo]);
            this.btCopiarIcone = "fas fa-copy";
            this.btCopiarTexto = "Copiar";
        },

        existemVersiculosSelecionados: function() {
            if(this.versiculosSelecionados !== undefined) {
                return this.versiculosSelecionados.some((item) => {return item});
            }
            return false;
        },

        copiarTexto: async function() {
            let texto = '';
            for(let i = 0; i<this.versiculos.length; i++) {
                if(this.versiculosSelecionados[i]) {
                    texto = texto.concat(this.versiculos[i]);
                }
            }
            // cleaning text
            texto = texto.replace(/\n+|[0-9]+\.|\r+|\t+|\s+/g, " ");
            texto = texto.replace(/\s+/g, " ");
            texto = texto.trim();

            let versiculosStr = this.computarSelecaoVersiculosFormatada();            

            texto = `"${texto}"\n\n(${this.livroSelecionado.slice(3)} ${this.formatChapterNumber(this.capituloSelecionado)}, ${versiculosStr})`;
            
            await navigator.clipboard.writeText(texto);

            this.btCopiarIcone = "fas fa-check";
            this.btCopiarTexto = "Copiado!";
        },

        computarSelecaoVersiculosFormatada: function() {
            // formatando seleção de versiculos
            let versiculosStr = `${this.versiculosSelecionados.indexOf(true)+1}`;
            let first = this.versiculosSelecionados.indexOf(true); 
            let last = first;
            
            for(let i = last+1; i<this.versiculosSelecionados.length; i++) {
                if(this.versiculosSelecionados[i]) {
                    if(i-1 === last) {
                        last = i;
                    }
                    else {
                        if(last !== first) {
                            versiculosStr = versiculosStr.concat(`-${last+1}`);
                        }
                        first = i;    
                        versiculosStr = versiculosStr.concat(`.${first+1}`);    
                        last = first;
                    }
                }
            }
            if(last !== first) {
                versiculosStr = versiculosStr.concat(`-${last+1}`);
            }
            return versiculosStr;
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
            this.versiculosSelecionados = undefined
            this.versiculos = undefined;
        },

        voltarParaCapitulos: function() {
            this.versiculos = undefined;
            this.versiculosSelecionados = undefined;
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
            versiculos: undefined,
            versiculosSelecionados: undefined,
            btCopiarTexto: "Copiar",
            btCopiarIcone: "fas fa-copy"
        }
    }


}
</script>
