<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <!-- <h1>中国电影翻译库</h1> -->
        <img src = "../../src/assets/top.jpg">
        <hr />
        <br />
        <br />
        <alert :message=message v-if="showMessage"></alert>


        <button type="button" class="btn btn-success btn-sm" v-b-modal.book-modal>添加数据</button>
        <br />
        <br />
        <table class="table table-hover">

          <thead>
            <tr>
              <th scope="col">编号</th>
              <th scope="col">电影原名</th>
              <th scope="col">电影译名</th>
              <th scope="col">原片语种</th>
              <th scope="col">翻译类型</th>
              <th scope="col">原产国</th>
              <th scope="col">电影类型</th>
              <th scope="col">导演</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(book, index) in books" :key="index">
              <td>{{ book.idfilms }}</td> 
              <td>{{ book.name_original }}</td>
              <td>{{ book.name_translation }}</td>
              <td>{{ book.language }}</td>
              <td>{{ book.type_translation }}</td>
              <td>{{ book.country_original }}</td>
              <td>{{ book.type }}</td>
              <td>{{ book.director_original }}</td>
              <td>
                <span v-if="book.read">Yes</span>
                <span v-else>No</span>
              </td>
              <td>
                <div class="btn-group" role="group">
                  <button
                          type="button"
                          class="btn btn-warning btn-sm"
                          v-b-modal.book-update-modal
                          @click="editBook(book)">
                      修改
                  </button> 
                  <button
                          type="button"
                          class="btn btn-danger btn-sm"
                          @click="onDeleteBook(book)">
                      删除
                  </button>


                  <!-- <button type="button" class="btn btn-warning btn-sm">Update</button>
                  <button type="button" class="btn btn-danger btn-sm ml-2">Delete</button>
                   -->
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <b-modal ref="addBookModal" id="book-modal" title="增加新的电影" hide-footer>
      <b-form @submit="onSubmit" @reset="onReset" class="w-100">
        <b-form-group id="form-title-group" label="电影原名:" label-for="form-title-input">
          <b-form-input
            id="form-title-input"
            type="text"
            v-model="addBookForm.title"
            required
            placeholder="请输入电影原名"
          ></b-form-input>
        </b-form-group>
        <b-form-group id="form-author-group" label="原片语种:" label-for="form-author-input">
          <b-form-input
            id="form-author-input"
            type="text"
            v-model="addBookForm.author"
            required
            placeholder="请输入原片语种"
          ></b-form-input>
        </b-form-group>
        <b-form-group id="form-read-group">
          <b-form-checkbox-group v-model="addBookForm.read" id="form-checks">
            <b-form-checkbox value="true">Read?</b-form-checkbox>
          </b-form-checkbox-group>
        </b-form-group>
        <b-button-group>
          <b-button type="submit" variant="primary">提交</b-button>
          <b-button type="reset" variant="danger">取消</b-button>
        </b-button-group>
      </b-form>
    </b-modal>
    <b-modal ref="editBookModal"
         id="book-update-modal"
         title="Update"
         hide-footer>
  <b-form @submit="onSubmitUpdate" @reset="onResetUpdate" class="w-100">
  <b-form-group id="form-title-edit-group"
                label="电影原名:"
                label-for="form-title-edit-input">
      <b-form-input id="form-title-edit-input"
                    type="text"
                    v-model="editForm.title"
                    required
                    placeholder="请输入电影原名">
      </b-form-input>
    </b-form-group>
    <b-form-group id="form-author-edit-group"
                  label="原片语种:"
                  label-for="form-author-edit-input">
        <b-form-input id="form-author-edit-input"
                      type="text"
                      v-model="editForm.author"
                      required
                      placeholder="请输入原片语种">
        </b-form-input>
      </b-form-group>
    <b-form-group id="form-read-edit-group">
      <b-form-checkbox-group v-model="editForm.read" id="form-checks">
        <b-form-checkbox value="true">Read?</b-form-checkbox>
      </b-form-checkbox-group>
    </b-form-group>
    <b-button-group>
      <b-button type="submit" variant="primary">更新</b-button>
      <b-button type="reset" variant="danger">取消</b-button>
    </b-button-group>
  </b-form>
</b-modal>

  </div>
</template>

<script>
import axios from "axios";
import Alert from './Alert.vue';

export default {
  data() {
    return {
      books: [],
      addBookForm:{
        title: "",
        author: "",
        read: []
      },
      editForm:{
        id:"",
        title:"",
        author:"",
        read:[]
      },
      message:'',
      showMessage:false,
    };
  },
  components:{
    alert:Alert,
  },
  methods: {
    getBooks() {
      const path = "http://localhost:5000/books";
      // const path = "http://localhost:5000/";
      axios
        .get(path)
        .then(res => {
          // this.books = res.data.books;
          this.books = res.data.books;
        })
        .catch(error => {
          // eslint-disable-next-line
          console.error(error);
        });
    },
    editBook(book) {
      this.editForm = book;
    },
    onSubmitUpdate(evt) {
      evt.preventDefault();
      this.$refs.editBookModal.hide();
      let read = false;
      if (this.editForm.read[0]) read = true;
      const payload = {
        title: this.editForm.title,
        author: this.editForm.author,
        read,
      };
      this.updateBook(payload, this.editForm.id);
    },
    updateBook(payload, bookID) {
      const path = `http://localhost:5000/books/${bookID}`;
      axios.put(path, payload)
        .then(() => {
          this.getBooks();
          this.message = '电影信息已更新!';
          this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getBooks();
        });
    },
    onResetUpdate(evt) {
      evt.preventDefault();
      this.$refs.editBookModal.hide();
      this.initForm();
      this.getBooks(); // 由于上面initForm清空了表单中的数据,同时双向绑定的效果也将当前的book对象数据给清空了,因此需要重新再get一次
    },

    addBook(payload) {
      const path = "http://localhost:5000/books";
      axios
        .post(path, payload)
        .then(() => {
          this.getBooks();
          this.message = '电影信息已添加!';
          this.showMessage = true;
        })
        .catch(error => {
          // eslint-disable-next-line
          console.log(error);
          this.getBooks();
        });
    },
    removeBook(bookID) {
      const path = `http://localhost:5000/books/${bookID}`;
      axios.delete(path)
        .then(() => {
          this.getBooks();
          this.message = '电影信息已删除!';
          this.showMessage = true;
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.error(error);
          this.getBooks();
        });
    },
    onDeleteBook(book) {
      this.removeBook(book.id);
    },

    initForm() {
      this.addBookForm.title = '';
      this.addBookForm.author = '';
      this.addBookForm.read = [];
      this.editForm.id = '';
      this.editForm.title = '';
      this.editForm.author = '';
      this.editForm.read = [];
    },

    onSubmit(evt) {
      evt.preventDefault();
      this.$refs.addBookModal.hide();
      let read = false;
      if (this.addBookForm.read[0]) read = true;
      const payload = {
        title: this.addBookForm.title,
        author: this.addBookForm.author,
        read // property shorthand
      };
      this.addBook(payload);
      this.initForm();
    },
    onReset(evt) {
      evt.preventDefault();
      this.$refs.addBookModal.hide();
      this.initForm();
    }
  },
  created() {
    this.getBooks();
  }
};
</script>
