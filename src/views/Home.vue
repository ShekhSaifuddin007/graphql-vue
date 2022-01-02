<template>
    <div class="home">
<!--        <ApolloQuery :query="require('@/graphql/queries/categories.gql')">-->
        <ApolloQuery :query="booksCategoriesQuery">
            <template slot-scope="{ result: { data }, isLoading }">
                <div v-if="isLoading">Loading...</div>
                <div v-else>
                    <a
                        href="#"
                        class="link"
                        @click.prevent="filterBy('all')"
                    >
                        All
                    </a>
                    <a
                        href="#"
                        class="link"
                        @click.prevent="filterBy('featured')"
                    >
                        Featured
                    </a>

                    <a
                        href="#"
                       v-for="category of data.categories"
                       :key="category.id"
                       @click.prevent="filterBy(category.id)"
                       class="link"
                    >
                        {{ category.id }} | {{ category.name }}
                    </a>
                </div>
            </template>
        </ApolloQuery>

        <ApolloQuery v-if="selectedCategory === 'all'" :query="query" :variables="{ id: selectedCategory }">
            <template slot-scope="{ result: { data }, isLoading }">
                <div v-if="isLoading">Loading...</div>
                <div v-else>
                    <div v-for="book of data.books" :key="book.id" class="link">
                        {{ book.id }} | {{ book.title }}
                    </div>
                </div>
            </template>
        </ApolloQuery>

        <ApolloQuery v-else-if="selectedCategory === 'featured'" :query="query" :variables="{ featured: true }">
            <template slot-scope="{ result: { data }, isLoading }">
                <div v-if="isLoading">Loading...</div>
                <div v-else>
                    <div v-for="book of data.featuredBooks" :key="book.id" class="link">
                        {{ book.id }} | {{ book.title }}
                    </div>
                </div>
            </template>
        </ApolloQuery>

<!--        <ApolloQuery :query="require('@/graphql/queries/books_by_category.gql')" :variables="{ id: selectedCategory }">-->
        <ApolloQuery v-else :query="query" :variables="{ id: selectedCategory }">
            <template slot-scope="{ result: { data }, isLoading }">
                <div v-if="isLoading">Loading...</div>
                <div v-else>
                    <div v-for="book of data.category.books" :key="book.id" class="link">
                        {{ book.id }} | {{ book.title }}
                    </div>
                </div>
            </template>
        </ApolloQuery>
    </div>
</template>

<script>
import booksCategoryQuery from '@/graphql/queries/books_by_category.gql'
import booksCategoriesQuery from '@/graphql/queries/categories.gql'
import booksQuery from '@/graphql/queries/books.gql'
import booksFeaturedQuery from '@/graphql/queries/featuredBooks.gql'

export default {
    name: 'Home',
    components: {},

    data () {
        return {
            booksCategoryQuery,
            booksCategoriesQuery,
            booksQuery,
            booksFeaturedQuery,
            selectedCategory: 'all',
            query: booksQuery,
        }
    },

    methods: {
        filterBy(category) {
            if (category === 'all') {
                this.query = booksQuery
            } else if (category === 'featured') {
                this.query = booksFeaturedQuery
            } else {
                this.query = booksCategoryQuery
            }

            this.selectedCategory = category
        }
    }
}
</script>

<style scoped>
.link {
    margin-right: 24px;
}
</style>
