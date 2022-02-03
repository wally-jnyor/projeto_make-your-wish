<template>
    <div>
        <div class="div-main">
            <Message :msg="msg" v-show="msg"/>
            <form id="burger-form" @submit="createBurger">
                <div class="input-container">
                    <label for="nome">Nome do cliente</label>
                    <input type="text" id="nome" name="nome" v-model="nome" placeholder="digite o seu nome">
                </div>
                 <div class="input-container">
                    <label for="pao">Escolha o tipo de Pão:</label>
                    <select name="pao" id="pao" v-model="pao">
                        <option value="">Selecione seu pão</option>
                        <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option>
                    </select> 
                </div>
                <div class="input-container">
                    <label for="carne">Escolha o tipo de Carne:</label>
                    <select name="carne" id="carne" v-model="carne">
                        <option value="">Selecione sua Carne</option>
                        <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option>
                    </select> 
                </div>
                <div id="opcionais-container" class="input-container">
                    <label id="opcionais-title" for="opcionais">Escolha Opcionais:</label>
                    <div class="opcionais-items">
                        <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id">
                            <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
                            <span>{{ opcional.tipo }}</span>
                        </div>
                    </div>
                </div>
                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Criar meu burger">
                </div>
            </form>
        </div>
    </div>
</template>

<script>
import Message from './Message.vue'

    export default {
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
                status: "Solicitado",
                msg: null
            }
        },
        methods: { // metodo para pegar os dados do banco de dados fake - metodo get
            async getIngredientes() {
                const req = await fetch("http://localhost:3000/ingredientes");
                const data = await req.json();

                this.paes = data.paes;
                this.carnes = data.carnes;
                this.opcionaisdata = data.opcionais;
            },
            async createBurger(e) {
                e.preventDefault()
                console.log("burger criado")

                const data = {
                    nome: this.nome,
                    carne: this.carne,
                    pao: this.pao,
                    opcionais: Array.from(this.opcionais),
                    status: "Solicitado"
                }
                console.log(data)
                const dataJson = JSON.stringify(data)
                const req = await fetch("http://localhost:3000/burgers", {
                    method: "POST",
                    headers: {"Content-Type": "application/json"},
                    body: dataJson
                })
                const res = await req.json()
                console.log(res)

                // feedback de pedido solicitado.
                this.msg = `Pedido Nº ${res.id} realizado com sucesso!`

                // limpar feedback de pedido apos exibição.
                setTimeout(() => this.msg = "", 3000);

                // limpar campos apos pedido.
                this.nome = "";
                this.carne = "";
                this.pao = "";
                this.opcionais = "";
            }
        },
        mounted(){
            this.getIngredientes()
        },
        components: {
            Message
        }
    }
</script>

<style scoped>
    .div-main {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    }
    .div-main p {
        margin-bottom: 20px;
    }

    #burger-form {
        width: 400px;
        margin: 0 auto;
    }
    .input-container {
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }
    label {
        font-weight: bold;
        margin-bottom: 10px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #FCBA03;
    }
    input, select {
        padding: 5px 10px;
        width: 100%;
    }
    #opcionais-container {
        flex-direction: column;
        width: 100%;
    }
    #opcionaus-title {
        width: 100%;
    }
    .opcionais-items {
        width: 100%;
        display: flex;
        flex-wrap: wrap;
    }
    .checkbox-container {
        display: flex;
        justify-content: flex-end;
        align-items: center;
        flex-direction: row-reverse;
        width: 50%;
        margin-bottom: 20px;
    }
    .checkbox-container span,
    .checkbox-container input {
        width: auto;
    }
    .checkbox-container span {
        margin-right: 8px;
        font-weight: 500;
    }
    .submit-btn {
        background-color: #222;
        color: #FCBA03;
        font-weight: 500;
        border: 2px solid #222;
        border-radius: 7px;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }
    .submit-btn:hover {
        background-color: transparent;
        color: #222;
        transition: .5s;
    }
</style>