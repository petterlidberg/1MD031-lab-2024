<template>
    <div class="burger">
        <h3>{{ burger.name }}</h3>
        <img width="175" height="175" :src="burger.url" :alt="burger.name" />
        <ul>
            <li>{{ burger.kCal }} kCal</li>
            <li v-if="burger.gluten">
                Contains <span class="allergy">gluten</span>
            </li>
            <li v-if="burger.lactose">
                Contains <span class="allergy">lactose</span>
            </li>
        </ul>
        <p>Ordered: {{ amountOrdered }}</p>
        <button @click="decrease">−</button>
        <button @click="increase">+</button>
    </div>
</template>

<script>
export default {
    name: "OneBurger",
    props: {
        burger: Object,
    },
    data() {
        return {
            amountOrdered: 0,
        };
    },
    methods: {
        increase() {
            this.amountOrdered++;
            this.$emit("update-order", {
                name: this.burger.name,
                amount: this.amountOrdered,
            });
        },

        decrease() {
            if (this.amountOrdered > 0) {
                this.amountOrdered--;
                this.$emit("update-order", {
                    name: this.burger.name,
                    amount: this.amountOrdered,
                });
            }
        },
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.burger h3 {
    margin-bottom: 0.5rem;
}
.burger button {
    margin: 0 0.15rem;
}
.burger p {
    color: yellow;
}
.burger img {
    display: block;
    margin: 0 auto 0.5rem auto;
}

.burger ul {
    padding-left: 1.2rem;
    text-align: left;
    margin: 0 auto;
    width: fit-content;
}

.allergy {
    font-weight: bold;
}
</style>
