<template>
    <div id="orders">
        <div id="orderList">
            <div
                v-for="(order, key) in orders"
                :key="'order' + key"
                class="order-box"
            >
                <strong>Order #{{ key }}</strong
                ><br />

                <strong>Burgers:</strong>
                <ul>
                    <li v-for="item in order.orderItems" :key="item">
                        {{ item }}
                    </li>
                </ul>

                <strong>Customer:</strong><br />
                Name: {{ order.customer?.name }} <br />
                Email: {{ order.customer?.email }} <br />
                Payment: {{ order.customer?.payment }} <br />
                Gender: {{ order.customer?.gender }} <br />
            </div>

            <button @click="clearQueue">Clear Queue</button>
        </div>

        <div id="dots">
            <div
                v-for="(order, key) in orders"
                :key="'dot' + key"
                :style="{
                    left: order.details.x + 'px',
                    top: order.details.y + 'px',
                }"
            >
                {{ key }}
            </div>
        </div>
    </div>
</template>
<script>
import io from "socket.io-client";
const socket = io("localhost:3000");

export default {
    name: "DispatcherView",
    data: function () {
        return {
            orders: null,
        };
    },
    created: function () {
        socket.on("currentQueue", (data) => (this.orders = data.orders));
    },
    methods: {
        clearQueue: function () {
            socket.emit("clearQueue");
        },
        changeStatus: function (orderId) {
            socket.emit("changeStatus", {
                orderId: orderId,
                status: "Annan status",
            });
        },
    },
};
</script>
<style>
#orderList {
    top: 1em;
    left: 1em;
    position: absolute;
    z-index: 2;
    color: black;
    background: rgba(255, 255, 255, 0.5);
    padding: 1em;
}
#dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width: 1920px;
    height: 1078px;
    cursor: crosshair;
    background-image: url("/img/polacks.jpg");
}

#dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width: 20px;
    height: 20px;
    text-align: center;
}
</style>
