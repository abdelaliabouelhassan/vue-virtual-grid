<template>
    <div class=" card" :style="{ height: 100 + '%' }">
        <span>
           title : {{ item.title }}   
            <br>
            id : {{ item.id }}
        </span>
    </div> 
</template>


<script>
export default {
    props: {
        item: {
            type: Object,
            required: true,
        },
    },
}
</script>


<style scoped>
.card {
    background-color: #fff;
    border-radius: 2px;
    display: inline-block;
    /* height: auto; */
    margin: 1rem;
    position: relative;
    width: 100%;
    height: auto;
    box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14),
        0 1px 5px 0 rgba(0, 0, 0, 0.12),
        0 3px 1px -2px rgba(0, 0, 0, 0.2);
    display: flex;
}

.card span {
    margin: auto;
    font-size: 2rem;
    padding: 10px;
}
</style>