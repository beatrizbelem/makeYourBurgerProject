<template>
  <div>
    <message :msg="msg" v-show="msg"></message>
  </div>
  <!-- eslint-disable-next-line vue/no-multiple-template-root -->
  <div class="forms">
    <form id="burger-form" @submit="createBurguer">
      <div class="input-container">
        <label for="name">Nome do cliente: </label>
        <input
          type="text"
          name="name"
          id="name"
          v-model="nome"
          placeholder="Digite o seu nome"
        />
      </div>
      <div class="input-container">
        <label for="bread">Escolha o pão:</label>
        <select name="bread" id="bread" v-model="pao">
          <option value="">Selecione o seu pão</option>
          <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">
            {{ pao.tipo }}
          </option>
        </select>
      </div>
      <div class="input-container">
        <label for="meat">Escolha a sua carne</label>
        <select name="meat" id="meat" v-model="carne">
          <option value="">Selecione a sua carne</option>
          <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">
            {{ carne.tipo }}
          </option>
        </select>
      </div>
      <div class="input-container" id="opcionais-container">
        <label for="opcionais" id="opcionais-title"
          >Selecione os opcionais:</label
        >
        <div
          class="checkbox-container"
          v-for="opc in opcionaisdata"
          :key="opc.id"
        >
          <input
            type="checkbox"
            name="opcionais"
            v-model="opc.selecionado"
            :value="opc.tipo"
          />
          <span>{{ opc.tipo }}</span>
        </div>
      </div>
      <input type="submit" class="submit-btn" value="criar meu burguer!" />
    </form>
  </div>
</template>
<script>
import Message from "./Message.vue";
export default {
  components: { Message },
  name: "BurgerForm",
  data() {
    return {
      paes: null,
      carnes: null,
      opcionaisdata: null,
      nome: null,
      pao: null,
      carne: null,
      opcionais: [],
      msg: null,
    };
  },
  methods: {
    async getIngredients() {
      const req = await fetch("http://localhost:3000/ingredientes"); // fazendo a requisição http
      const data = await req.json(); // esperando a resposta e armazenando na data
      this.paes = data.paes; //atribuindo paes(Data) para paes(vue)
      this.carnes = data.carnes;
      this.opcionaisdata = data.opcionais;
    },
    async createBurguer(e) {
      e.preventDefault();

      const data = {
        nome: this.nome,
        carne: this.carne,
        pao: this.pao,
        opcionais: this.opcionaisdata
          .filter((opc) => opc.selecionado)
          .map((opc) => opc.tipo),
        status: "Solicitado",
      };

      console.log(data);
      const dataJson = JSON.stringify(data); //transforma dado em texto
      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST", //o método
        headers: { "Content-Type": "application/json" }, //me comunicando com o json
        body: dataJson, //texto
      });
      const res = await req.json(); //enviando a requisicao
      this.msg = `Pedido Nº ${res.id} realizado com Sucesso!`;

      setTimeout(() => (this.msg = ""), 8000);

      this.nome = "";
      this.carne = "";
      this.pao = "";
      this.opcionais = "";
    },
  },
  mounted() {
    this.getIngredients();
  },
};
</script>
<style scoped>
.forms {
  display: flex;
  justify-content: center;
}
#burger-form {
  max-width: 400px;
  margin: 0 auto;
}

.input-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

label {
  font-weight: bold;
  margin-bottom: 15px;
  color: #222;
  padding: 5px 10px;
  border-left: 4px solid #fcba03;
}

input,
select {
  padding: 5px 10px;
  width: 300px;
}

#opcionais-container {
  flex-direction: row;
  flex-wrap: wrap;
}

#opcionais-title {
  width: 100%;
}

.checkbox-container {
  display: flex;
  align-items: flex-start;
  width: 50%;
  margin-bottom: 20px;
}

.checkbox-container span,
.checkbox-container input {
  width: auto;
}

.checkbox-container span {
  margin-left: 6px;
  font-weight: bold;
}

.submit-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.submit-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
