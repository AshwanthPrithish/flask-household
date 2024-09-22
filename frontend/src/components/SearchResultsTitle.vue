<template>
    <div>
        <h1>Search Results of Book Title</h1>
        <div v-if="message" class="alert alert-danger">{{ message }}</div>
        <div class="container" v-if="books.length > 0">
            <ul class="book-list">
                <li v-for="(book, index) in books" :key="index" class="book-card">
                    <div v-if="role === 'student' && !book.content">
                        <a class="btn btn-secondary btn-sm m-1" :href="`/request_book/${book.section_id}/${book.id}`">Request This Book</a>
                    </div>
                    <b>Title: </b>{{ book.title }}<br />
                    <b>Author: </b>{{ book.author }}<br />
                    <b>Language: </b>{{ book.lang }}<br />
                    <b>Section: </b>{{ book.section }}<br />
                    <div v-if="book.content">
                        <b>Content: </b>
                        <div class="card" style="height: 300px; overflow-y: auto;">
                            <div class="card-body">
                                <center><b>{{ book.title }}</b></center>
                                <center>By - <em>{{ book.author }}</em></center>
                                <hr />
                                {{ book.content }}
                            </div>
                        </div>
                        <br />
                    </div>
                    <b>Rating: </b>{{ book.rating }}<br />
                    <b>Released year: </b>{{ book.release_year }}<br />
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import { mapState } from 'vuex';

export default {
    props: {
        data: {
            type: String,
            required: true
        }
    },
    data() {
        return {
            books: [],
            message: null
        };
    },
    computed: {
        ...mapState(['role']), // Assuming role is stored in Vuex
        parsedData() {
            return JSON.parse(this.data); // Parse the JSON string into an object if needed
        }
    },
    created() {
        this.fetchBooks();
    },
    methods: {
        fetchBooks() {
            axios
                .post('/search-results-title', { title: this.parsedData.title })
                .then((response) => {
                    if (response.data.success) {
                        this.books = response.data.books;
                    } else {
                        this.message = response.data.message;
                    }
                })
                .catch(() => {
                    this.message = 'Error fetching books.';
                });
        }
    }
};
</script>

<style scoped>
.book-list {
    list-style-type: none; /* Remove default list styles */
    padding: 0; /* Remove default padding */
}

.book-card {
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 16px;
    margin: 16px 0;
    background-color: #f9f9f9;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
</style>
