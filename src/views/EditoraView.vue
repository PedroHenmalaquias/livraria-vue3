<script setup>
import { ref, reactive, onMounted } from "vue";
import EditorasApi from "@/api/editoras";
const editorasApi = new EditorasApi();

const defaultEditora = { id: null, nome: "", site: ""};
const editoras = ref([]);
const editora = reactive({ ...defaultEditora });

onMounted(async () => {
  editoras.value = await editorasApi.buscarTodasAsEditoras();
});

function limpar() {
  Object.assign(editora, { ...defaultEditora });
}

async function salvar() {
  if (editora.id) {
    await editorasApi.atualizarEditora(editora);
  } else {
    await editorasApi.adicionarEditora(editora);
  }
  editoras.value = await editorasApi.buscarTodasAsEditoras();
  limpar();
}

function editar(editora_para_editar) {
  Object.assign(editora, editora_para_editar);
}

async function excluir(id) {
  await editorasApi.excluirEditora(id);
  editoras.value = await editorasApi.buscarTodasAsEditoras();
  limpar();
}
</script>

<template>
  <h1>Editora</h1>
  <hr />
  <div class="form">
    <input type="text" v-model="editora.nome" placeholder="Nome" />
    <input type="text" v-model="editora.site" placeholder="Site" />
    <button @click="salvar">Salvar</button>
    <button @click="limpar">Limpar</button>
  </div>
  <hr />
  <ul>
    <li v-for="editora in editoras" :key="editora.id">
      <span @click="editar(editora)">
        ({{ editora.id }}) - {{ editora.nome }} -
      </span>
      <button @click="excluir(editora.id)">X</button>
    </li>
  </ul>
</template>

<style></style>
