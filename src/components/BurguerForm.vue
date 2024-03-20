<template>
    <div>
        <Message :msg="msg" v-show="msg"/>
        <div>
            <form id="burguer-form" @submit="createBurguer">
                <div class="input-container">
                    <label for="clientName">Nome do Cliente:</label>
                    <input type="text" id="clientName" name="clientName" v-model="clientName" placeholder="Digite o seu nome">
                </div>
                <div class="input-container">
                    <label for="bread">Escolha o pão:</label>
                    <select name="bread" id="bread" v-model="bread">
                        <option value="Selecione o seu pão">Selecione o seu pão</option>
                        <option v-for="bread in breads" key="bread.id" :value="bread.tipo">{{ bread.tipo }}</option>
                    </select>
                </div>
                <div class="input-container">
                    <label for="meat">Escolha a carne do seu burguer:</label>
                    <select name="meat" id="meat" v-model="meat">
                        <option value="Selecione o tipo de carne">Selecione o tipo de carne</option>
                        <option v-for="meat in meats" key="meat.id" :value="meat.tipo">{{ meat.tipo }}</option>
                    </select>
                </div>
                <div id="choice-container" class="input-container">
                    <label id="choice-title" for="choice">Selecione os opcionais:</label>
                    <div class="checkbox-container" v-for="choices in choicedata" key="choices.id">
                        <input type="checkbox" name="choice" v-model="choice" :value="choices.tipo">
                        <span>{{ choices.tipo }}</span>
                    </div>
                </div>
                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Criar meu Burguer!">
                </div>
            </form>
        </div>
    </div>
</template>

<script>
import Message from './Message.vue'

export default {
    name: 'BurguerForm',
    components: {
      Message
    },
    data() {
      return {
        breads: null,
        meats: null,
        choicedata: null,
        clientName: null,
        bread: "Selecione o seu pão",
        meat: "Selecione o tipo de carne",
        choice: [],
        status: "Solicitado",
        msg: null
      }
    },
    methods: {
      async getIngredients() {
        const req = await fetch("http://localhost:3000/ingredientes");
        const data = await req.json();

        this.breads = data.paes;
        this.meats = data.carnes;
        this.choicedata = data.opcionais;
      },
      async createBurguer(e) {
        e.preventDefault();

        const data = {
          clientName: this.clientName,
          bread: this.bread,
          meat: this.meat,
          choice: Array.from(this.choice),
          status: "Solicitado"
        };

        const dataJson = JSON.stringify(data);

        const req = await fetch("http://localhost:3000/burgers", {
          method: "POST",
          headers: {
            "Content-Type": "application/json"
          },
          body: dataJson
        });

        const res = await req.json();

        //colocar mensagem de sistema
        this.msg = `Pedido Nº ${res.id} realizado com sucesso!`;

        //limpar msg
        setTimeout(() => this.msg="", 3000)

        //limpar os campos
        this.clientName = "";
        this.bread = "";
        this.meat = "";
        this.choice = "";

      }
    },
    mounted() {
      this.getIngredients()
    }
}
</script>

<style scoped>
#burguer-form {
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
    color: #222;;
    padding: 5px 10px;
    border-left: 4px solid #fcba03;
  }

  input, select {
    padding: 5px 10px;
    width: 300px;
  }

  #choice-container {
    flex-direction: row;
    flex-wrap: wrap;
  }

  #choice-title {
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
    color:#fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .submit-btn:hover {
    background-color: transparent;
    color: #222;
  }
</style>