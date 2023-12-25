<template>
    <div class="container">
        <template v-if="!isProductUnavailable">
            <div class="background-shape"></div>
        </template>
        <div class="flex-container" :class="getBackgroundClass">
            <div class="product-card">
                <template v-if="this.loading">
                    <div class="product-container">
                        <div class="skeleton-image product-image"></div>
                        <div class="skeleton-details product-details">
                            <header></header>
                            <main></main>
                            <footer></footer>
                        </div>
                    </div>
                </template>
                <template v-else>
                    <template v-if="isProductUnavailable">
                        <div class="unavailable-product-container">
                            <div class="unavailable-product-background">
                                <img src="../assets/img/bg-sad-edited.svg" alt="" />
                            </div>
                            <div class="unavailable-product-details">
                                <p>This product is unavailable to show</p>
                                <div class="cta-button">
                                    <button class="cta-button-next" @click="nextProduct">Next product</button>
                                </div>
                            </div>
                        </div>
                    </template>
                    <template v-else>
                        <div class="product-container">
                            <div class="product-image">
                                <img :src="product.image" alt="Product Image" />
                            </div>
                            <section class="product-details">
                                <header>
                                    <h2 :class="getTextClass">{{ product.title }}</h2>
                                    <section class="product-tag">
                                        <span>{{ product.category }}</span>
                                        <div class="rating">
                                            <p>{{ product.rate }}/5</p>
                                            <div class="rating-circles">
                                                <template v-for="n in 5">
                                                    <span v-if="n <= getProductRate" class="circle" :class="[getBackgroundColorClass, getCircleBorderClass]" :key="n"></span>
                                                    <span v-else class="circle-white" :class="getCircleBorderClass" :key="n"></span>
                                                </template>
                                            </div>
                                        </div>
                                    </section>
                                </header>
                                <main>
                                    <p>{{ product.description }}</p>
                                </main>
                                <footer>
                                    <h3 :class="getTextClass">${{ product.price }}</h3>
                                    <div class="cta-button">
                                        <button class="cta-button-buy" :class="[getBackgroundColorClass, getButtonBorderClass]">Buy now</button>
                                        <button class="cta-button-next" :class="[getTextClass, getButtonBorderClass]" @click="nextProduct">Next product</button>
                                    </div>
                                </footer>
                            </section>
                        </div>
                    </template>
                </template>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "ProductDisplay",
    data() {
        return {
            product: {},
            currentIndex: 1,
            loading: null,
        };
    },
    methods: {
        async getDataProductByIndex(index) {
            try {
                this.loading = true;

                const response = await fetch(`https://fakestoreapi.com/products/${index}`);
                const data = await response.json();
                this.product = data;
                this.product.rate = this.product.rating.rate;

                this.loading = false;
            } catch (e) {
                console.error(e);
            }
        },
        nextProduct() {
            if (this.currentIndex >= 20) this.currentIndex = 1;
            else this.currentIndex++;
            this.getDataProductByIndex(this.currentIndex);
        },
    },
    mounted() {
        this.getDataProductByIndex(this.currentIndex);
    },
    computed: {
        getProductRate() {
            return Math.floor(this.product.rate);
        },
        isProductUnavailable() {
            const availableProduct = this.product.category === "men's clothing" || this.product.category === "women's clothing";
            return !availableProduct;
        },
        getTextClass() {
            return this.product.category === "men's clothing" ? "men-text-color" : "women-text-color";
        },
        getButtonBorderClass() {
            return this.product.category === "men's clothing" ? "men-button-border-color" : "women-button-border-color";
        },
        getBackgroundClass() {
            if (this.loading) {
                return "unavailable-background";
            } else if (this.product.category === "men's clothing") {
                return "men-background";
            } else if (this.product.category === "women's clothing") {
                return "women-background";
            }
            return "unavailable-background";
        },
        getBackgroundColorClass() {
            return this.product.category === "men's clothing" ? "men-background-color" : "women-background-color";
        },
        getCircleBorderClass() {
            return this.product.category === "men's clothing" ? "men-circle-border-color" : "women-circle-border-color";
        },
    },
};
</script>
