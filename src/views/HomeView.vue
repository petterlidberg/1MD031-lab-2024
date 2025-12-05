<template>
    <header>
        <h1>Välkommen till BurgerHeaven</h1>
    </header>
    <main>
        <section class="burger-section">
            <h2>Select burger</h2>
            <p>This is where you execute burger selection</p>
            <div class="burger-grid">
                <Burger
                    v-for="burger in burgers"
                    :key="burger.name"
                    :burger="burger"
                    @update-order="updateOrderedBurgers"
                />
            </div>
        </section>

        <section class="customer-section">
            <h2>Customer information</h2>
            <p>This is where you provide necessary information</p>

            <h3>Delivery information:</h3>

            <form>
                <p>
                    Full name<br />
                    <input
                        type="text"
                        name="fullname"
                        placeholder="First- and Last name"
                    />
                </p>

                <p>
                    E-mail<br />
                    <input
                        type="email"
                        name="email"
                        placeholder="E-mail address"
                    />
                </p>

                <p>Where living?</p>
                <div id="map-container">
                    <div id="map" @click="setLocation">
                        <div
                            class="target"
                            :style="{
                                left: location.x + 'px',
                                top: location.y + 'px',
                            }"
                        >
                            T
                        </div>
                    </div>
                </div>

                <p>
                    Payment options<br />
                    <select name="payment">
                        <option>Credit card</option>
                        <option>Invoice</option>
                        <option>Cash on delivery</option>
                    </select>
                </p>

                <p>
                    Gender<br />
                    <label>
                        <input type="radio" name="gender" value="male" />
                        Male
                    </label>
                    <br />

                    <label>
                        <input type="radio" name="gender" value="female" />
                        Female
                    </label>
                    <br />

                    <label>
                        <input type="radio" name="gender" value="nonbinary" />
                        Non-binary
                    </label>
                    <br />

                    <label>
                        <input
                            type="radio"
                            name="gender"
                            value="undisclosed"
                            checked
                        />
                        Do not wish to provide
                    </label>
                </p>
                <button type="submit" class="order-button">
                    <svg
                        class="scooter-icon"
                        xmlns="http://www.w3.org/2000/svg"
                        viewBox="0 0 200 120"
                        width="150"
                        height="150"
                    >
                        <g
                            fill="none"
                            stroke="#0b7a53"
                            stroke-width="4"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                        >
                            <circle cx="50" cy="90" r="18" />
                            <circle cx="145" cy="90" r="18" />

                            <rect
                                x="25"
                                y="22"
                                width="55"
                                height="35"
                                rx="4"
                                ry="4"
                            />

                            <path d="M35 65 H105" />

                            <path
                                d="M35 65
										   Q60 60 75 70
										   Q85 75 95 82
										   H120
										   Q135 82 145 72"
                            />

                            <path
                                d="M145 72
										   Q135 50 135 35
										   H150
										   Q160 35 160 45
										   V55"
                            />

                            <path d="M135 30 H155" />

                            <circle cx="160" cy="35" r="6" />
                        </g>
                    </svg>
                    Place my order!
                </button>
            </form>
        </section>
    </main>
    <footer>
        <hr />
        <p>&copy; 2025 Lidberg Burgers Inc.</p>
    </footer>
</template>

<script>
import Burger from "../components/OneBurger.vue";
import io from "socket.io-client";
import menu from "../assets/menu.json";

const socket = io("localhost:3000");

function MenuItem(name, kCal, url, gluten, lactose) {
    this.name = name;
    this.kCal = kCal;
    this.url = url;
    this.gluten = gluten;
    this.lactose = lactose;
}

const burgerMenuItems = [
    new MenuItem("Barger", 300, "/img/fire-burger.png", false, true),
    new MenuItem("Birger", 400, "/img/fried-turkey-burger.png", true, true),
    new MenuItem("Berger", 500, "/img/double-cheese-burger.png", false, false),
];

// console.log(burgerMenuItems);

export default {
    name: "HomeView",
    components: {
        Burger,
    },
    data: function () {
        return {
            burgers: menu,
            burgerText: "Välj en burgare",
            fullName: "",
            email: "",
            streetName: "",
            streetNumber: "",
            payment: "SMS lån",
            gender: "burgir",
            orderedBurgers: {},

            location: {
                x: 0,
                y: 0,
            },
        };
    },
    methods: {
        getOrderNumber: function () {
            return Math.floor(Math.random() * 100000);
        },
        updateOrderedBurgers(data) {
            this.orderedBurgers[data.name] = data.amount;
        },
        setLocation(event) {
            const offset = {
                x: event.currentTarget.getBoundingClientRect().left,
                y: event.currentTarget.getBoundingClientRect().top,
            };

            this.location.x = event.clientX - offset.x;
            this.location.y = event.clientY - offset.y;

            console.log("New location", this.location);
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
@import url("https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap");

#map-container {
    width: auto;
    height: 400px;
    overflow: scroll;
    border: 3px dashed grey;
    margin: 1rem auto;
}

#map {
    position: relative;
    width: 1920px;
    height: 1080px;
    background: url("/img/polacks.jpg");
    background-size: contain;
}

html {
    font-size: 16px; /* ensures 1rem = 16px reference */
}

body {
    font-size: 1rem; /* 12pt ≈ 1rem baseline */
    font-family: arial;
    color: black;
}

main,
header,
footer,
nav ul {
    max-width: 40rem;
    margin: 0 auto;
}

header {
    background-image: url("../img/polacks.jpg");
    background-size: cover;
    overflow: hidden;
    width: 100%;
    height: 12.5rem; /* 200px */
    opacity: 0.5;
}

header h1 {
    width: 40rem;
    margin: 0 auto;
    text-align: center;
}

h2 {
    font-size: 1.5rem; /* 18pt */
}

h3 {
    font-size: 1.083rem; /* 13pt */
}

section {
    padding: 0.75rem;
    margin: 0.5rem 0;
}

.burger-section {
    background-color: black;
    color: white;
    border: 0.15rem dotted white;
}

.burger-section p {
    color: white;
}

.burger-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1rem;
    margin-top: 1rem;
}

.burger {
    text-align: center;
}

.customer-section {
    border: 0.15rem dotted black;
}

input[type="radio"] {
    height: 0.7rem;
    width: 0.7rem;
}

.order-button {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;

    width: 15rem;
    height: 6rem;

    padding: 0.5rem;
    background-color: #e6e6e6;
    border: 0.125rem solid #000; /* 2px */
    border-radius: 1rem;

    margin-top: 2rem;

    font-size: 1rem;
    font-weight: bold;
    color: #000;

    cursor: pointer;
    transition:
        background-color 0.2s ease,
        color 0.2s ease;
}

.scooter-icon {
    width: 6.25rem; /* 100px */
    height: auto;
    margin-bottom: 0.3rem;
}

.scooter-icon g {
    transition: stroke 0.2s ease;
}

.order-button:hover {
    background-color: #0b7a53;
    color: #fff;
}

.order-button:hover .scooter-icon g {
    stroke: #fff;
}
</style>
