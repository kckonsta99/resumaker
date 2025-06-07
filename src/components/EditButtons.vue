<template>
    <button v-if="showAddBtn" class="edit-btn add" @click="$emit('addClick')">
        {{ textAdd }}
    </button>
    <button v-if="showRemoveBtn" class="edit-btn remove" @click="$emit('removeClick')">
        {{ textRemove }}
    </button>
</template>

<script>
export default {
    props: {
        'showRemoveBtn': {
            default: true,
            type: Boolean
        }, 
        'showAddBtn': {
            default: true,
            type: Boolean
        },
        textAdd: {
            default: "+"
        },
        textRemove: {
            default: "-"
        }
    }
}
</script>

<style scoped>

.edit-off .edit-btn {
    display: none;
}

.edit-btn {
    min-width: 20px;
    height: 20px;
    border-radius: 4px;
    border: 1px solid transparent;
    background-color: none;
    margin-left: 8px;
    transition-duration: 200ms;
    cursor: pointer;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    transform: scale(1.1);
}

.add {
    background-color: #0dea9f;
    font-weight: bold;
    color: #000;
}

.add:hover {
    opacity: 0.75;
}

.remove {
    background-color: #ff6f61;
    font-weight: bold;
    color: #000;
}

.remove:hover {
    opacity: 0.75;
    
}


</style>