<template>
  <div class="body">
    <header class="header">Тест</header>
    <main class="container">
      <aside class="sidebar">
        <form action="" class="form">
          <label for="product-title" class="title form_title"
            >Выберите продукцию</label
          >
          <v-select
            v-model="form.selected"
            id="product-title"
            label="title"
            :options="productList"
            class="input form_input title--blue"
          ></v-select>

          <label for="product-quantity" class="title form_title">
            Введите количество
          </label>
          <input
            type="number"
            id="product-quantity"
            min="1"
            v-model="form.quantity"
            required
            class="input form_input title--blue"
          />
          <button
            class="button form_button button--blue"
            @click.prevent="addCart"
          >
            Добавить
          </button>
        </form>
      </aside>

      <section class="content">
        <table class="table content__table w-100">
          <tbody>
            <tr class="d-flex mb-6 d-sm-none">
              <td class="title title--blue flex-grow">Название товара</td>
              <td
                class="
                  title title--blue
                  w-20
                  title--align-right
                  min-fit-content
                "
              >
                Количество
              </td>
              <td
                class="
                  title title--blue
                  w-20
                  title--align-right
                  min-fit-content
                "
              >
                Стоимость
              </td>
            </tr>
            <tr
              class="d-flex mb-3 flex-sm-wrap"
              v-for="item in cart"
              :key="item.id"
            >
              <td class="title flex-grow w-sm-100">
                {{ item.title }}
              </td>
              <td
                class="
                  title
                  w-20
                  title--align-right title--align-sm-left
                  min-fit-content
                  w-sm-50
                "
              >
                {{ item.quantity }} шт.
              </td>
              <td class="title w-20 title--align-right w-sm-50 min-fit-content">
                {{ item.price.toFixed(2) }}
              </td>
            </tr>
          </tbody>
        </table>
        <div class="title w-100 title--align-right mt-6">
          <span> <span class="d-sm-none">Итого: </span>{{ totalPrice }}</span>
        </div>
        <div class="d-flex justify-center mt-6">
          <button
            class="button button--green"
            @click.prevent="saveCart"
            :disabled="isDisabledButtonSave"
          >
            Сохранить
          </button>
        </div>

        <div class="d-flex justify-center mt-6" v-if="orderNumber">
          <span class="title">Ваш номер заказа: {{ orderNumber }}</span>
        </div>
      </section>
    </main>

    <footer class="footer d-flex flex-column">
      <div class="d-flex justify-end mt-a flex-grow">
        <span class="title footer__title title--white title--align-right"
          >Тест</span
        >
      </div>
    </footer>
  </div>
</template>

<script>
export default {
  title: "MainPage",
  data() {
    return {
      productList: [],
      cart: [],
      form: {
        selected: null,
        quantity: null,
      },
      totalPrice: 0,
      orderNumber: null,
    };
  },

  async mounted() {
    const data = await fetch(
      "https://dev-su.eda1.ru/test_task/products.php"
    ).then((r) => r.json());
    this.productList = data.products;
  },

  methods: {
    addCart() {
      if (this.form.quantity > 0) {
        const product = this.productList.find(
          (item) => item.id === this.form.selected.id
        );

        // const findProduct = this.cart.find((item) => item.id === product.id);

        // if (findProduct) {
        // console.log(findProduct);
        // } else {
        this.cart.push({
          id: Date.now(),
          title: product.title,
          quantity: this.form.quantity,
          price: product.price * this.form.quantity,
        });
        // }

        this.form.selected = null;
        this.form.quantity = null;
      }
    },

    async saveCart() {
      const data = await fetch("https://dev-su.eda1.ru/test_task/save.php", {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
        },
        body: JSON.stringify(this.cart),
      })
        .then((r) => r.json())
        .catch((e) => console.log(e.json()));

      this.orderNumber = data.code;
    },
  },

  computed: {
    isDisabledButtonSave() {
      if (this.cart.length > 0) {
        return false;
      } else {
        return true;
      }
    },
  },

  watch: {
    cart() {
      const totalPrice = this.cart.reduce((acc, item) => {
        return acc + item.price;
      }, null);

      this.totalPrice = totalPrice.toFixed(2);
    },
  },
};
</script>
