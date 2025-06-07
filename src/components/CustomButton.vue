<template>
    <button class="btn" :class="customClass">
        <slot></slot>
    </button>
</template>

<script>
export default {
    props: {
        btnType: {
            type: String,
            default: ""
        }
    },
    computed: {
        customClass() {
            if(this.btnType) {
                return 'btn-' + this.btnType;
            }
        }
    }
}
</script>

<style scoped>
.btn{
    border-radius: 4px;
    padding: 8px 10px;
    border: none;
    font-size: 16px;
    display: block;
    width: 100%;
    background-color: #82C0CC;
}

.btn:hover {
    opacity: 0.7;
    cursor: pointer;
}

.btn-primary {
    background-color: var(--color-green);
    color: white;
}

.btn-primary:hover {
    opacity: 0.7;
}

.btn-secondary {
    background: #ffb347;
    background: -webkit-linear-gradient(to right, #ffcc33, #ffb347);
    background: linear-gradient(to right, #ffcc33, #ffb347);
    /* color: white; */
    font-weight: 600;
}

.btn-secondary:hover {
    opacity: 0.7;
}

</style>