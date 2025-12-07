<template>
    <header>
        <h1>Welcome to BurgerHeaven Online</h1>
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
                        v-model="fullName"
                    />
                </p>

                <p>
                    E-mail<br />
                    <input
                        type="email"
                        name="email"
                        placeholder="E-mail address"
                        v-model="email"
                    />
                </p>

                <p>Please indicate your point of delivery</p>
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
                    <select name="payment" v-model="payment">
                        <option>Credit card</option>
                        <option>Invoice</option>
                        <option>Cash on delivery</option>
                    </select>
                </p>

                <p>
                    Gender<br />
                    <label>
                        <input
                            type="radio"
                            id="male"
                            value="male"
                            v-model="gender"
                        />
                        Male
                    </label>
                    <br />

                    <label>
                        <input
                            type="radio"
                            id="female"
                            value="female"
                            v-model="gender"
                        />
                        Female
                    </label>
                    <br />

                    <label>
                        <input
                            type="radio"
                            id="nonbinary"
                            value="nonbinary"
                            v-model="gender"
                        />
                        Non-binary
                    </label>
                    <br />

                    <label>
                        <input
                            type="radio"
                            id="undisclosed"
                            value="undisclosed"
                            v-model="gender"
                        />
                        Do not wish to provide
                    </label>
                </p>
                <button type="submit" class="order-button" @click="placeOrder">
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

// not in use
// console.log(burgerMenuItems);

export default {
    name: "HomeView",
    components: {
        Burger,
    },
    data: function () {
        return {
            burgers: menu,
            orderedBurgers: {},
            fullName: "",
            email: "",
            payment: "Credit card",
            gender: "undisclosed",
            location: {
                x: -50,
                y: -50,
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

            this.location.x = event.clientX - offset.x - 10;
            this.location.y = event.clientY - offset.y - 10;

            console.log("New location", this.location);
        },
        placeOrder() {
            const items = Object.entries(this.orderedBurgers)
                .filter(([name, amount]) => amount > 0)
                .map(([name, amount]) => `${name} (${amount})`);

            console.log("Sending order:", {
                orderId: this.getOrderNumber(),
                details: { x: this.location.x, y: this.location.y },
                orderItems: items,
                customer: {
                    name: this.fullName,
                    email: this.email,
                    payment: this.payment,
                    gender: this.gender,
                },
            });
            // alert("Order placed");
            socket.emit("addOrder", {
                orderId: this.getOrderNumber(),
                details: {
                    x: this.location.x,
                    y: this.location.y,
                },
                orderItems: items,
                customer: {
                    name: this.fullName,
                    email: this.email,
                    payment: this.payment,
                    gender: this.gender,
                },
            });
        },
    },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap");

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
    background-image: url("/img/burger-joint.png");
    background-size: cover;
    background-position: center center;
    background-repeat: no-repeat;
    overflow: hidden;
    width: 100%;
    height: 12.5rem;
    position: relative;
    margin: 0 auto;
    height: 200px;
}

header h1 {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);

    font-family: "Agbalumo";
    font-size: 2rem;
    color: rgba(255, 255, 255, 1);
    text-align: center;
    margin: 0;
    z-index: 10;
    text-wrap: balance;
    line-height: 1;
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

#map-container {
    width: auto;
    height: 400px;
    overflow: scroll;
    border: 0.15rem dashed grey;
    margin: 0.5rem auto;
    padding: 0;
}

#map {
    position: relative;
    width: 1920px;
    height: 1080px;
    background: url("/img/polacks.jpg");
    background-size: contain;
    margin: 0;
    padding: 0;
}
.target {
    position: absolute;
    background-color: black;
    border-radius: 50%;
    color: white;
    font-size: 0.75rem;
    width: 1.25rem;
    height: 1.25rem;
    text-align: center;
    line-height: 1.25rem;
}
</style>
