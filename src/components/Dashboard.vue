<template>
  <div>
    <message :msg="msg" v-show="msg"></message>

  <div id="burguer-table">
    <div>
      <div id="burguer-table-heading">
        <div class="order-id">#</div>
        <div>Cliente:</div>
        <div>Pão:</div>
        <div>Carne:</div>
        <div>Opcionais:</div>
        <div>Ações:</div>
      </div>
    </div>

    <div id="burguer-table-rows">
      <div class="burguer-table-row" v-for="burger in burgers" :key="burger.id">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.nome }}</div>
        <div>{{ burger.pao }}</div>
        <div>{{ burger.carne }}</div>
        <div>
          <ul>
            <li v-for="(opcional, index) in burger.opcionais" :key="index">
              {{ opcional }}
            </li>
          </ul>
        </div>
        <div>
          <select
            name="status"
            class="status"
            @change="updatedBurger($event, burger.id)"
          >
            <option
              v-for="s in status"
              :key="s.id"
              :value="s.tipo"
              :selected="burger.status === s.tipo"
            >
              {{ s.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deleteBurger(burger.id)">
            Cancelar
          </button>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import Message from "./Message.vue";

export default {
  components: {
    Message,
  },
  name: "Dashboard",
  data() {
    return {
      burgers: null,
      burguer_id: null,
      status: [],
      msg: null,
    };
  },
  methods: {
    async getPedidos() {
      const req = await fetch("http://localhost:3000/burgers");
      const data = await req.json();
      this.burgers = data;
      console.log(this.burgers);

      this.getStatus();
    },
    async getStatus() {
      const req = await fetch("http://localhost:3000/status");
      const data = await req.json();
      this.status = data;
    },
    async deleteBurger(id) {
      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: "DELETE",
      });

      const res = await req.json();

      this.msg = `Pedido Nº ${res.id} Removido com sucesso!`;

      setTimeout(() => (this.msg = ""), 8000);

      this.getPedidos();
    },
    async updatedBurger(event, id) {
      const option = event.target.value; // saber o status que o usuario utiliza
      const dataJson = JSON.stringify({ status: option });
      const req = await fetch(`http://localhost:3000/burgers/${id}`, {
        method: "PATCH",
        headers: { "Content-Type": "application/json" },
        body: dataJson,
      });

      const res = await req.json();

      this.msg = `Pedido Nº ${res.id} foi atualizado para ${res.status}!`;

      setTimeout(() => (this.msg = ""), 8000);
    },
  },
  mounted() {
    this.getPedidos();
  },
};
</script>

<style scoped>
#burguer-table {
  max-width: 1200px;
  margin: 0 auto;
}
.burguer-table-row,
#burguer-table-heading,
#burguer-table-rows {
  display: flex;
  flex-wrap: wrap;
}
#burguer-table-heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}
#burguer-table-heading div,
.burguer-table-row div {
  width: 19%;
}

.burguer-table-row {
  width: 100%;
  padding: 12px;
  border-bottom: 1px solid #ccc;
}
.burguer-table-row .order-number,
#burguer-table-heading .order-id {
  width: 5%;
}

select {
  padding: 12px 6px;
  margin-right: 12px;
}

.delete-btn {
  background-color: #222;
  color: #fcba03;
  border: 2px solid #222;
  padding: 10px;
  font-style: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}
.delete-btn:hover {
  background-color: transparent;

  color: #222;
}
</style>
