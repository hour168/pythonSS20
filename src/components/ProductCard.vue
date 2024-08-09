<template>
    <div class="col-custom">
        <div class="card mb-4 box-shadow">
            <img class="card-img-top" :src="product.image" alt="Card image cap">
            <div class="card-body">
                <h5 class="card-title">{{ truncatedTitle }}</h5>
                <p class="card-text">
                    {{ truncatedDescription }}
                    <span v-if="isDescriptionLong" class="more" @click="toggleDescription">
                        {{ showFullDescription ? 'See less' : 'See more' }}
                    </span>
                </p>
                <div class="d-flex justify-content-between align-items-center mt-3">
                    <small class="text-muted price">Price: ${{ product.price }}</small>
                    <button :class="{'btn btn-outline-danger': isInCart, 'btn btn-outline-primary': !isInCart}" @click="toggleCart(product)">
                        {{ isInCart ? 'Remove from Cart' : 'Add to Cart' }}
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: ['product'],
        data() {
            return {
                showFullDescription: false,
                isInCart: false
            };
        },
        computed: {
            truncatedTitle() {
                const title = this.product.title || '';
                return title.length <= 19 ? title : title.slice(0, 19) + '...';
            },
            truncatedDescription() {
                const description = this.product.description || '';
                return this.showFullDescription || description.length <= 100 ? description : description.slice(0, 100) + '...';
            },
            isDescriptionLong() {
                return this.product.description && this.product.description.length > 100;
            }
        },
        methods: {
            toggleCart(product) {
                let cartItems = JSON.parse(localStorage.getItem('cartItems')) || [];

                if (this.isInCart) {
                    cartItems = cartItems.filter(item => item.id !== product.id);
                    this.isInCart = false;
                } else {
                    product.quantity = 1;
                    cartItems.push(product);
                    this.isInCart = true;
                }

                localStorage.setItem('cartItems', JSON.stringify(cartItems));
                this.$root.$emit('update-cart', cartItems.length);
            },
            toggleDescription() {
                this.showFullDescription = !this.showFullDescription;
            }
        },
        created() {
            let cartItems = JSON.parse(localStorage.getItem('cartItems')) || [];
            this.isInCart = cartItems.some(item => item.id === this.product.id);
        }
    }
</script>

<style scoped>
    .col-custom {
        flex: 0 0 20%;
        max-width: 20%;
        padding: 0 5px;
    }

    .card {
        height: 480px;
        display: flex;
        flex-direction: column;
        overflow: hidden;
        padding: 2px;
        margin-bottom: 5px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        border: none;
        border-radius: 10px;
        transition: transform 0.3s;
    }

    .card:hover {
        transform: scale(1.05);
    }

    .card-img-top {
        width: 100%;
        height: 200px;
        object-fit: cover;
        border-top-left-radius: 10px;
        border-top-right-radius: 10px;
    }

    .card-body {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        height: calc(100% - 220px);
        padding: 1rem;
    }

    .card-title {
        font-size: 1.25rem;
        font-weight: bold;
        margin-bottom: 10px;
    }

    .card-text {
        font-size: 0.875rem;
        margin-bottom: 20px;
        line-height: 1.4;
    }

    .btn {
        border: 2px solid;
        padding: 0.5rem 1rem;
        font-size: 0.875rem;
        border-radius: 5px;
        transition: background-color 0.3s, color 0.3s;
    }

    .btn-outline-primary {
        border-color: #007bff;
        color: #007bff;
    }

    .btn-outline-primary:hover {
        background-color: #007bff;
        color: white;
    }

    .btn-outline-danger {
        border-color: red;
        color: red;
    }

    .btn-outline-danger:hover {
        background-color: red;
        color: white;
    }

    .price {
        font-size: 1rem;
        font-weight: bold;
        color: #333;
    }

    .more {
        cursor: pointer;
        color: #007bff;
        font-weight: bold;
    }

    @media (max-width: 1200px) {
        .col-custom {
            flex: 0 0 33.33%;
            max-width: 33.33%;
        }
    }

    @media (max-width: 768px) {
        .col-custom {
            flex: 0 0 50%;
            max-width: 50%;
        }
    }

    @media (max-width: 576px) {
        .col-custom {
            flex: 0 0 100%;
            max-width: 100%;
        }
    }
</style>
