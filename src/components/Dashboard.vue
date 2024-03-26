<template>
    <div id="burguer-table">
        <Message :msg="msg" v-show="msg" />
        <div>
            <div id="burguer-table-heading">
                <div class="order-id">#:</div>
                <div>Cliente:</div>
                <div>Pão:</div>
                <div>Carne:</div>
                <div>Opcionais:</div>
                <div>Ações:</div>
            </div>
        </div>
        <div id="burguer-table-rows">
            <div class="burguer-table-row" v-for="burguer in burguers" :key="burguer.id">
                <div class="order-number">{{ burguer.id }}</div>
                <div>{{ burguer.clientName }}</div>
                <div>{{ burguer.bread }}</div>
                <div>{{ burguer.meat }}</div>
                <div>
                    <ul>
                        <li v-for="(choice, index) in burguer.choice" :key="index">
                            {{ choice }}
                        </li>
                    </ul>
                </div>
                <div>
                    <select name="status" class="status" @change="updateBurguer($event, burguer.id)">
                        <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="burguer.status == s.tipo">
                            {{ s.tipo }}
                        </option>
                    </select>
                    <button class="delete-btn" @click="deleteBurguer(burguer.id)">Cancelar</button>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
import Message from "./Message.vue"

export default {
    name: "Dashboard",
    components: {
        Message
    },
    data() {
        return {
            burguers: null,
            burguer_id: null,
            status: [],
            msg: null
        }
    },
    methods: {
        async getPedidos() {
            const req = await fetch("http://localhost:3000/burgers");

            const data = await req.json();

            this.burguers = data;

            //resgatar status
            this.getStatus();
        },
        async getStatus() {
            const req = await fetch("http://localhost:3000/status");

            const data = await req.json();

            this.status = data;
        },
        async deleteBurguer(id) {
            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "DELETE"
            });

            const res = await req.json();

            //msg de pedido deletado
            this.msg = `Pedido Nº ${res.id} removido com sucesso!`;

            //limpar msg
            setTimeout(() => this.msg = "", 3000)

            this.getPedidos();
        },
        async updateBurguer(event, id) {
            const option = event.target.value;

            const dataJson = JSON.stringify({ status: option });

            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "PATCH",
                headers: { "Content-Type": "application/json" },
                body: dataJson
            });

            const res = await req.json();

            //colocar mensagem de sistema
            this.msg = `Pedido Nº ${res.id} foi atualizado para ${res.status}!`;

            //limpar msg
            setTimeout(() => this.msg = "", 3000)

            console.log(res);

        }
    },
    mounted() {
        this.getPedidos();
    }
}
</script>
<style scoped>
#burguer-table {
    max-width: 1200px;
    margin: 0 auto;
}

#burguer-table-heading,
#burguer-table-rows,
.burguer-table-row {
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
    border-bottom: 1px solid #CCC;
}

#burguer-table-heading .order-id,
.burguer-table-row .order-number {
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
    border: 2px solid #222;
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