<template>
  <div class="container is-widescreen">
    <section class="section" v-if="error">
      <div class="container is-widescreen">
        <div class="notification is-danger">
          <!-- <%= error.code + ': ' + error.sqlMessage %> -->
          <!---->
          {{ error }}
        </div>
      </div>
    </section>
    <section class="hero">
      <div class="hero-body">
        <p class="title">Create new Blog</p>
      </div>
    </section>
    <section class="px-6">
      <input
        class="mb-5"
        multiple
        type="file"
        accept="image/png, image/jpeg, image/webp"
        @change="selectImages"
      />

      <div v-if="images" class="columns is-multiline">
        <div
          v-for="(image, index) in images"
          :key="image.id"
          class="column is-one-quarter"
        >
          <div class="card">
            <div class="card-image">
              <figure class="image is-4by3">
                <img :src="showSelectImage(image)" alt="Placeholder image" />
              </figure>
            </div>
            <footer class="card-footer">
              <a
                @click="deleteSelectImage(index)"
                class="card-footer-item has-text-danger"
                >Delete</a
              >
            </footer>
          </div>
        </div>
      </div>

      <div class="field mt-5">
        <label class="label">Title</label>
        <div class="control">
          <input
            v-model="$v.titleBlog.$model"
            class="input"
            type="text"
            placeholder="Text input"
          />
        </div>
        <template v-if="$v.titleBlog.$error">
          <p class="help is-danger" v-if="!$v.titleBlog.required">
            Title is required
          </p>
          <p class="help is-danger" v-if="!$v.titleBlog.alpha">
            Title must be alphabets
          </p>
          <p class="help is-danger" v-if="!$v.titleBlog.maxLength">
            Title length must not exceed 25
          </p>
          <p class="help is-danger" v-if="!$v.titleBlog.minLength">
            Title length must not least then 10
          </p>
        </template>
      </div>

      <div class="field">
        <label class="label">Content</label>
        <div class="control">
          <textarea
            v-model="$v.contentBlog.$model"
            class="textarea"
            placeholder="Textarea"
          ></textarea>
        </div>
        <template v-if="$v.titleBlog.$error">
          <p class="help is-danger" v-if="!$v.contentBlog.required">
            Content is required
          </p>
          <p class="help is-danger" v-if="!$v.contentBlog.minLength">
            Content length must be atleast 50
          </p>
        </template>
      </div>

      <div class="field">
        <label class="label">Reference</label>
        <div class="control">
          <input
            class="input"
            type="url"
            v-model="$v.reference.$model"
            placeholder="e.g. https://www.google.com"
          />
        </div>
        <template v-if="$v.reference.$error">
          <p class="help is-danger" v-if="!$v.reference.url">
            Reference must be a valid URL
          </p>
        </template>
      </div>

      <div class="control mb-3">
        <label class="radio">
          <input
            v-model="$v.statusBlog.$model"
            type="radio"
            name="answer"
            value="status_private"
          />
          Private
        </label>
        <label class="radio">
          <input
            v-model="$v.statusBlog.$model"
            type="radio"
            name="answer"
            value="status_public"
          />
          Public
        </label>
      </div>
      <template v-if="$v.statusBlog.$error">
        <p class="help is-danger" v-if="!$v.statusBlog.customCheck">
          Status is required
        </p>
      </template>

      <div class="field">
        <div class="control">
          <label class="checkbox">
            <input v-model="pinnedBlog" type="checkbox" />
            Pinned
          </label>
        </div>
      </div>

      <hr />

      <div class="columns">
        <div class="column">
          <div class="field">
            <label class="label">วันที่โพสต์</label>
            <div class="control">
              <input class="input" type="date" v-model="$v.start_date.$model" />
            </div>
            <template v-if="$v.start_date.$error">
              <p
                class="help is-danger"
                v-if="!$v.start_date.customStartDateCheck"
              >
                Start date must be specified
              </p>
              <p
                class="help is-danger"
                v-if="!$v.start_date.isLessThanEndDate"
              >
                Start date must be less than end date
              </p>
            </template>
          </div>
        </div>
        <div class="column">
          <div class="field">
            <label class="label">วันสิ้นสุดโพสต์</label>
            <div class="control">
              <input class="input" type="date" v-model="$v.end_date.$model" />
            </div>
            <template v-if="$v.end_date.$error">
              <p class="help is-danger" v-if="!$v.end_date.customEndDateCheck">
                End date must be specified
              </p>
              <p class="help is-danger" v-if="!$v.end_date.isMoreThanStartDate">
                End date must be more than start date
              </p>
            </template>
          </div>
        </div>
      </div>
      <div class="field is-grouped">
        <div class="control">
          <button @click="submitBlog" class="button is-link">Submit</button>
        </div>
        <div class="control">
          <button @click="$router.go(-1)" class="button is-link is-light">
            Cancel
          </button>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
import {
  required,
  alpha,
  minLength,
  maxLength,
  url,
  ref,
  helpers
} from "vuelidate/lib/validators";

export default {
  data() {
    return {
      blog: {},
      error: null,
      images: [], // array of image
      titleBlog: "",
      contentBlog: "",
      statusBlog: "status_public",
      reference: "",
      pinnedBlog: false,
      start_date: "",
      end_date: ""
    };
  },
  validations: {
    titleBlog: {
      required,
      alpha,
      minLength: minLength(10),
      maxLength: maxLength(25)
    },
    contentBlog: {
      required,
      minLength: minLength(50)
    },
    statusBlog: {
      customCheck: value => {
        if (["status_private", "status_public"].includes(value)) {
          return true;
        }

        return false;
      }
    },
    reference: {
      url
    },
    start_date: {
      customStartDateCheck: (value, vm) => {
        if (!vm.start_date && vm.end_date) {
          return false;
        }

        return true;
      },
      isLessThanEndDate: (value, vm) => {
        if(!vm.end_date){
          return true // ข้ามไปก่อน
        }

        if (value && value > vm.end_date) {
          return false;
        }

        return true;
      }
    },
    end_date: {
      customEndDateCheck: (value, vm) => {
        if (!vm.end_date && vm.start_date) {
          return false;
        }

        return true;
      },
      isMoreThanStartDate: (value, vm) => {
        if(!vm.start_date){
          return true // ข้ามไปก่อน
        }

        if (value && value < vm.start_date) {
          return false;
        }

        return true;
      }
    }
  },
  methods: {
    selectImages(event) {
      this.images = event.target.files;
    },
    showSelectImage(image) {
      // for preview only
      return URL.createObjectURL(image);
    },
    deleteSelectImage(index) {
      console.log(this.images);
      this.images = Array.from(this.images);
      this.images.splice(index, 1);
    },
    submitBlog() {
      this.$v.$touch();

      if (this.$v.$invalid) {
        return;
      }

      let formData = new FormData();
      formData.append("title", this.titleBlog);
      formData.append("content", this.contentBlog);
      formData.append("pinned", this.pinnedBlog ? 1 : 0);
      formData.append("reference", this.reference);
      formData.append("start_date", this.start_date);
      formData.append("end_date", this.end_date);
      formData.append("status", this.statusBlog);
      this.images.forEach(image => {
        formData.append("myImage", image);
      });

      // Note ***************
      // ตอนเรายิง Postmant จะใช้ fromData
      // ตอนยิงหลาย ๆ รูปพร้อมกันใน Postman จะเป็นแบบนี้

      // title   | "This is a title of blog"
      // comment | "comment in blog"
      // ...
      // myImage | [select file 1]
      // myImage | [select file 2]
      // myImage | [select file 3]

      // จะสังเกตุว่าใช้ myImage เป็น key เดียวกัน เลยต้องเอามา loop forEach
      // พอไปฝั่ง backend มันจะจัด file ให้เป็น Array เพื่อเอาไปใช้งานต่อได้

      // Earth : อ๋ออ

      if (Array.from(this.images).some(file => file.size > 1024 * 1024)) {
        alert("File is too big");
        return;
      }

      console.log({
        title: this.titleBlog,
        content: this.contentBlog,
        pinned: this.pinnedBlog,
        reference: this.reference,
        start_date: this.start_date,
        end_date: this.end_date,
        status: this.statusBlog,
        myImage: this.images
      });

      axios
        .post("http://localhost:3000/blogs", formData, {
          headers: {
            "Content-Type": "application/x-www-form-urlencoded"
          }
        })
        .then(res => {
          this.$router.push({ name: "home" });
        })
        .catch(e => {
          const responseData = e.response.data;
          const onlyMessage = responseData?.message;

          if (onlyMessage) {
            alert(onlyMessage);
            return;
          }

          alert(
            responseData.details
              .map(detail => "- " + detail.message + "\n")
              .join(" ")
          );

          // console.log(e.response.data)
        });
    }
  }
};
</script>

<style></style>
