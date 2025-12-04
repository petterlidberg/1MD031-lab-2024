<template>
    <div>
        <div>
            <h1>Burgers</h1>
            <Burger
                v-for="burger in burgers"
                v-bind:burger="burger"
                v-bind:key="burger.name"
            />
        </div>
        <div id="map" v-on:click="addOrder">click here</div>
    </div>
</template>

<script>
import Burger from "../components/OneBurger.vue";
import io from "socket.io-client";

const socket = io("localhost:3000");

function MenuItem(name, kCal, url, gluten, lactose) {
    this.name = name;
    this.kCal = kCal;
    this.url = url;
    this.gluten = gluten;
    this.lactose = lactose;
}

const burgerMenuItems = [
    new MenuItem("Barger", 300, "pic", false, true),
    new MenuItem("Birger", 400, "pic", true, true),
    new MenuItem("Berger", 500, "pic", false, false),
];

console.log(burgerMenuItems);

export default {
    name: "HomeView",
    components: {
        Burger,
    },
    data: function () {
        return {
            burgers: burgerMenuItems,
        };
    },
    methods: {
        getOrderNumber: function () {
            return Math.floor(Math.random() * 100000);
        },
        addOrder: function (event) {
            var offset = {
                x: event.currentTarget.getBoundingClientRect().left,
                y: event.currentTarget.getBoundingClientRect().top,
            };
            socket.emit("addOrder", {
                orderId: this.getOrderNumber(),
                details: {
                    x: event.clientX - 10 - offset.x,
                    y: event.clientY - 10 - offset.y,
                },
                orderItems: ["Beans", "Curry"],
            });
        },
    },
};
</script>

<style>
#map {
    width: 300px;
    height: 300px;
    background-color: red;
}
</style>
