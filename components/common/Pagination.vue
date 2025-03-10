<template>
    <nav aria-label="Page navigation">
        <ul class="pagination mb-0">
            <li class="page-item" @click="changePage({ direction: 'previous' })" role="button">
                <span class="page-link fw-bold arrow-button bg-dark-2" tabindex="-1" aria-disabled="true"><ChevronLeft /></span>
            </li>
            <li v-for="n in pages" :key="n" :role="n === morePagesPlaceholder ? '' : 'button'" class="page-item" :class="{ 'active': currentPage === n, 'disabled': n === morePagesPlaceholder }" @click="changePage({ pageNo: n })"><span class="page-list-item bg-dark-2 page-link fw-bold">{{ n }}</span></li>
            <li class="page-item" @click="changePage({ direction: 'next' })" role="button">
                <span class="page-link fw-bold arrow-button bg-dark-2"><ChevronRight /></span>
            </li>
        </ul>
    </nav>
</template>

<script>
import ChevronLeft from "vue-material-design-icons/ChevronLeft.vue"
import ChevronRight from "vue-material-design-icons/ChevronRight.vue"

export default {
    components: { ChevronLeft, ChevronRight },
    data() {
        return {
            currentPage: 1,
            morePagesPlaceholder: "..."
        }
    },
    props: {
        totalPages: {
            type: Number,
            required: true
        },
    },
    mounted() {
        if(this.$route.query.page) {
            this.currentPage = parseInt(this.$route.query.page)
        }
    },
    methods: {
        changePage(event) {
            if(event.direction && event.direction === 'previous' && this.currentPage > 1) {
                this.currentPage--
            }
            else if(event.direction && event.direction === 'next' && this.currentPage < this.totalPages) {
                this.currentPage++
            }
            else if(event.pageNo && event.pageNo !== this.morePagesPlaceholder) {
                this.currentPage = event.pageNo
            }
        },
        paginate(current_page, last_page) {
            let pages = [];
            for (let i = 1; i <= last_page; i++) {
                let offset = 1;
                if (i === 1 || (current_page - offset <= i && current_page + offset >= i) || i === current_page || i === last_page) {
                    pages.push(i);
                } else if (i === current_page - (offset + 1) || i === current_page + (offset + 1)) {
                    pages.push(this.morePagesPlaceholder);
                }
            }
            return pages;
        }
    },
    watch: {
        currentPage(value) {
            this.$emit('onPageChange', value)
        },
        totalPages() {
            this.currentPage = 1
        }
    },
    computed: {
        pages() {
            return this.paginate(this.currentPage, this.totalPages)
        }
    }
}
</script>

<style scoped lang="scss">
@import "../../assets/styles/variable";
.pagination {
    li {
        margin-right: 0.5rem;
        position: relative;
        z-index: 2;
    }
    .active span {
        background-color: $primary-1 !important;
    }

    .page-list-item {
        border-radius: 4px;
        border: $block-border;
        padding: 8px 16px;
        color: $white;
        text-align: center;
        font-family: $font-family-sans-serif;
        font-size: 14px;
        font-style: normal;
        font-weight: 700;
        line-height: 22px;

        &:hover {
            border-color: $primary-1;
        }
    }

    .active {
        .page-list-item {
            border-color: $primary-1;
        }
    }

    .arrow-button {
        padding: 1px 8px;
        font-size: 24px;
        border-color: $black-3;
        color: $white;

        &:hover {
            border-color: $primary-1;
        }

        &:focus {
            box-shadow: none;
            background-color: $primary-1;
        }
    }
}
</style>