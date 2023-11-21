<template>
    <div class="gerencie-vocabulos">
        <h4>Buscar Vocabulos</h4>
        <div>
            <div>
                <label >Termo</label>
                <input type="text" name="" id="" v-model="buscar_termo"/>
            </div>
            <div>
                <label >Versao</label>
                <input type="text" name="" id="" v-model="buscar_versao"/>
            </div>
            <div>
                <button @click="buscar()">Buscar</button>
                <button @click="limpar()">Limpar</button>
            </div>
       
        </div>

        <p>Lista de Vocábulos </p>

            <div>
                <label>TERMO: </label>
                <input type="text" name="" id="" v-model="termo"/>
            </div>
            <div>              
                <label>SIGNIFICADO: </label>
                <input type="text" name="" id="" v-model="significado"/>
            </div>
            <div>            
                <label>VERSÃO: </label>
                <input type="number" name="" id="" v-model="versao"/>
            </div>
            <div>
                <button @click="cadastrarVocabulo">Adicionar</button>
            </div>
            <p v-if="erro">{{ erro }}</p>

        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Termo</th>
                    <th>Significado</th>
                    <th>Versão</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="vocabulo in vocabulos" :key="vocabulo.id">
                    <td>{{ vocabulo.id }}</td>
                    <td>{{ vocabulo.termo }}</td>
                    <td>{{ vocabulo.significado }}</td>
                    <td>{{ vocabulo.versao }}</td>
                </tr>
            </tbody>
        </table>
        <!-- <p v-if="erro">{{ erro }}</p> -->
    </div>
  </template>
  <style>
    td {
        padding: 20px;
    }
</style>
  <script setup lang="ts">
  import { RouterLink } from 'vue-router'
  import { computed } from 'vue';
  import { onMounted } from 'vue'
  import axios from 'axios'   
  import { ref } from 'vue'
  import { useRoute } from 'vue-router';
  
  const termo = ref('')
  const significado = ref('')
  const versao = ref()
  const buscar_termo = ref('')
  const buscar_versao = ref()

  const vocabulos = ref([])

  var erro = ref()

  var currentDate = new Date();

  async function cadastrarVocabulo() {
    try {
        await axios.post('vocabulo', {
            "termo": termo.value,
            "significado": significado.value,
            "versao": versao.value,
            "dataHoraCadastro": currentDate
        })
        

    } catch (error) {
        erro.value = (error as Error).message;
    }
    getVocabulos()
    limpaForm()
  }

  async function getVocabulos() {
        vocabulos.value = (await axios.get('vocabulo')).data
  }

  async function limpaForm() {
    termo.value = ''
    significado.value = ''
    versao.value = null
    console.log(termo.value)
    console.log(significado.value)
    console.log(versao.value)
  }
async function buscar() {
    if(buscar_termo.value == '' || buscar_versao.value == null){
        erro.value = 'Ambos os campos de termo e versão necessitam ser preenchidos para realizar a busca'
        vocabulos.value = []
    }
    else {
        try {
        vocabulos.value = (await axios.get(`vocabulo/${buscar_termo.value}/${buscar_versao.value}`)).data 
        
    } catch (error) {
        erro.value = 'Nenhum vocábulo foi encontrado para os critérios fornecidos'
    }
    }

    
}

async function limpar() {
    buscar_termo.value = ''
    buscar_versao.value = null
    getVocabulos()
    erro.value = ''
}

  onMounted(() => {
    getVocabulos()
})
  
  </script>