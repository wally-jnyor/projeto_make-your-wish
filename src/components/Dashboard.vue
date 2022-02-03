<template>
    <div id="burger-table">
        <Message :msg="msg" v-show="msg"/>
        <div>
            <div id="burger-table-heading">
                <div class="order-id">#:</div>
                <div>Client:</div>
                <div>Pão:</div>
                <div>Carne:</div>
                <div>Opicionais:</div>
                <div>Ações:</div>
            </div>
        </div>

        <div id="burger-table-rows">
            <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
                <div class="order-number">{{ burger.id }}</div>
                <div>{{ burger.nome }}</div>
                <div>{{ burger.pao }}</div>
                <div>{{ burger.carne }}</div>
                <div>
                    <ul>
                        <li v-for="(opcionais, index) in burger.opcionais" :key="index">{{ opcionais }}</li>
                    </ul>
                </div>
                <select name="status" class="status" @change="updateBurger($event, burger.id)">
                    <option value="">selecione o status do hamburger</option>
                    <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo"> {{ s.tipo }} </option>
                </select>
                <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
            </div>
        </div>
    </div>
</template>

<script>
import Message from './Message.vue'

  export default {
    name: "Dashboard",
    data() {
      return {
        burgers: null,
        burger_id: null,
        status: [],
        msg: null
      }
    },
    components: {
        Message
    },
    methods: {
      //resgatar os pedidos
      async getPedidos() {
        const req = await fetch("http://localhost:3000/burgers");
        const data = await req.json();
        this.burgers = data;
        //console.log(this.burgers)

        this.getStatus();
      },
      //resgatar os status
      async getStatus() {
        const req = await fetch("http://localhost:3000/status");
        const data = await req.json();
        this.status = data;
        //console.log(data)
      },
      async deleteBurger(id) {
        const req = await fetch(`http://localhost:3000/burgers/${id}`, {
            method: "DELETE"
        })
        const res = await req.json()
        //console.log(id)
               
        //MSg de pedido deletado
        this.msg = `Pedido de Nº ${res.id} foi Cancelado`
        setTimeout(() => this.msg = "", 3000)

        // recarrega novamente a lista de pedidos
        this.getPedidos()
      },
      async updateBurger(event, id) {
        const option = event.target.value
        const dataJson = JSON.stringify({ status: option })

        const req = await fetch(`http://localhost:3000/burgers/${id}`,  {
            method: "PATCH",
            headers: { "content-type": "application/json" },
            body: dataJson
        })
        const res = await req.json()
        console.log(res.id)

        //Msg de status do pedido atualizado
        this.msg = `Status do pedido Nº ${res.id} atualizado para ${res.status}`
        setTimeout(() => this.msg = "", 3000)
      }
    },
    mounted() {
        this.getPedidos();
    }
}
</script>

<style scoped>
    #burger-table {
        max-width: 1200px;
        margin: 0 auto;
    }

    #burger-table-heading,
    #burger-table-rows,
    .burger-table-row {
        display: flex;
        flex-wrap: wrap;
    }

    #burger-table-heading {
        font-size: bold;
        padding: 12px;
        border-bottom: 3px solid #333;
    }

    #burger-table-heading div,
    .burger-table-row div {
        width: 16%;
    }

    .burger-table-row {
        width: 100%;
        padding: 12px;
        border-bottom: solid 1px #CCC;
    }

    #burger-table-heading .order-id,
    .burger-table-row .order-number {
        width: 5%;
    }

    select {
        padding: 12px 6px;
        margin-right: 12px;
    }

    .delete-btn {
        background-color: #222;
        color: #FCBA03;
        font-weight: bold;
        border: solid 2px #222;
        padding: 10px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }

    .delete-btn:hover {
        background-color: transparent;
        color: #222;
    }
</style>