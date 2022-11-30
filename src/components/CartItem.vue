<template>
    <tr>
        <td><router-link :to="item.product.get_absolute_url">{{ item.product.name }}</router-link></td>
        <td>${{ item.product.price }}</td>
        <td>
            <a @click="decreamentQuantity(item)">-</a>
            {{ item.quantity }}
            <a @click="increamentQuantity(item)">+</a>
        </td>
        <td>${{ getItemTotal(item).toFixed(2) }}</td>
        <td><button @click="removeFromCart(item)" class="delete"></button></td>
    </tr>
</template>
<script>

export default{
    name:'ÇartItem',
    props:{
        initialItem:Object,
    },
    data(){
        return {
            item:this.initialItem
        }
    },
    methods:{
        getItemTotal(item){
            return item.quantity * item.product.price;
        },
        decreamentQuantity(item){
            item.quantity -= 1;

            if(item.quantity === 0){
                this.$emit('removeFromCart',item);
            }
            this.updateCart();
        },
        increamentQuantity(item){
            item.quantity += 1;

            this.updateCart();
        },
        updateCart(){
            localStorage.setItem('cart',JSON.stringify(this.$store.state.cart));
        },
        removeFromCart(item){
            this.$emit('removeFromCart',item);
            this.updateCart();
        }
    }
}
</script>