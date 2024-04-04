<script setup>
import { ref, reactive, onMounted } from "vue";
import LivrosApi from "@/api/livros";
import axios from "axios";
const livrosApi = new LivrosApi();

const defaultLivro = { id: null, titulo: "", isbn: "", quantidade: 0, preco: 0, categoria: "", editora: 0};
const livros = ref([]);
const livro = reactive({ ...defaultLivro });
const autores = ref([]);
const editoras = ref([]);
const categorias = ref([]);

async function buscarTodasAsCategorias() {
    const { data } = await axios.get("/categorias/");
    categorias.value = data
};
async function buscarTodasAsEditoras() {
    const { data } = await axios.get("/editoras/");
    editoras.value = data
};
async function buscarTodosOsAutores() {
    const { data } = await axios.get("/autores/");
    autores.value = data
};
onMounted(async () => {
    livros.value = await livrosApi.buscarTodosOsLivros();
});

function limpar() {
    Object.assign(livro, { ...defaultLivro });
}

async function salvar() {
    if (livro.id) {
        await livrosApi.atualizarLivro(livro);
    } else {
        await livrosApi.adicionarLivro(livro);
    }
    livros.value = await livrosApi.buscarTodosOsLivros();
    limpar();
}

function editar(livro_para_editar) {
    Object.assign(editora, livro_para_editar);
}

async function excluir(id) {
    await livrosApi.excluirLivro(id);
    livros.value = await livrosApi.buscarTodosOsLivros();
    limpar();
}
buscarTodosOsAutores();
buscarTodasAsEditoras();
buscarTodasAsCategorias();
</script>

<template>
    <h1>livro</h1>
    <hr />
    <div class="form">
        <input type="text" v-model="livro.titulo" placeholder="Título" />
        <input type="text" v-model="livro.isbn" placeholder="ISBN" />
        <input type="text" v-model="livro.quantidade" placeholder="Quantidade" />
        <input type="text" v-model="livro.preco" placeholder="Preço" />
        <select v-model="livro.editora"><option v-for="editora in editoras" :key="editora.id" :value="editora.id">{{ editora.id }}</option></select>
        <select v-model="livro.categoria"><option v-for="categoria in categorias" :key="categoria.id" :value="categoria.id">{{ categoria.descricao }}</option></select>
        <button @click="salvar">Salvar</button>
        <button @click="limpar">Limpar</button>
    </div>
    <hr />
    <ul>
        <li v-for="livro in livros" :key="livro.id">
            <span @click="editar(livro)">
                ({{ livro.id }}) - {{ livro.nome }} -
            </span>
            <button @click="excluir(livro.id)">X</button>
        </li>
    </ul>
</template>

<style></style>