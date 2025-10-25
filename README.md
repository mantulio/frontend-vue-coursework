<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Lesson Booking App</title>

  <!-- Bootstrap CSS -->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css"
  />
  <!-- Font Awesome -->
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css"
  />
  <!-- Vue.js 2 -->
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>

  <style>
    body {
      background-color: #f9fafc;
      padding: 20px;
    }
    .card {
      border-radius: 15px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    }
    .navbar {
      margin-bottom: 30px;
      border-radius: 12px;
    }
    .disabled-btn {
      opacity: 0.6;
      cursor: not-allowed;
    }
    .lesson-img {
      height: 160px;
      object-fit: cover;
      border-radius: 10px;
    }
    .container {
      max-width: 1200px;
    }
  </style>
</head>
<body>
  <div id="app">
    <!-- Navigation bar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary px-4">
      <a class="navbar-brand fw-bold" href="#">Lesson Booking</a>
      <div class="ms-auto">
        <button
          class="btn btn-light me-2"
          @click="showLessons = true"
          :disabled="showLessons"
        >
          <i class="fa fa-book"></i> Lessons
        </button>
        <button
          class="btn btn-light"
          @click="showLessons = false"
          :disabled="cart.length === 0"
        >
          <i class="fa fa-shopping-cart"></i> Cart ({{ cart.length }})
        </button>
      </div>
    </nav>


    <script>
    new Vue({
      el: '#app',
      data: {
        lessons: [],
        cart: [],
        showLessons: true,
        searchText: '',
        sortAttr: 'topic',
        sortDir: 'asc',
        order: { name: '', phone: '' },
        orderConfirmed: false
      },
      computed: {
        filteredAndSortedLessons() {
          // Filter
          let filtered = this.lessons.filter(l =>
            l.topic.toLowerCase().includes(this.searchText.toLowerCase()) ||
            l.location.toLowerCase().includes(this.searchText.toLowerCase())
          );

          // Sort
          return filtered.sort((a, b) => {
            let mod = this.sortDir === 'asc' ? 1 : -1;
            if (a[this.sortAttr] < b[this.sortAttr]) return -1 * mod;
            if (a[this.sortAttr] > b[this.sortAttr]) return 1 * mod;
            return 0;
          });
        },
        cartTotal() {
          return this.cart.reduce((sum, item) => sum + item.price, 0);
        },
        isFormValid() {
          return /^[A-Za-z\s]+$/.test(this.order.name) && /^[0-9]+$/.test(this.order.phone);
        }
      },
      methods: {
        addToCart(lesson) {
          this.cart.push(lesson);
          lesson.space--;
        },
        removeFromCart(index) {
          const lesson = this.cart[index];
          lesson.space++;
          this.cart.splice(index, 1);
        },
        checkout() {
          if (!this.isFormValid) return alert('Please fill valid details.');
          this.cart = [];
          this.orderConfirmed = true;
          this.showLessons = true;
        },
        imgError(event) {
          event.target.style.display = 'none';
        }
      },
      mounted() {
        fetch('lessons.json')
          .then(res => res.json())
          .then(data => (this.lessons = data))
          .catch(err => console.error('Error loading lessons.json', err));
      }
    });
  </script>
</body>
</html>
