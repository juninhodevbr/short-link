<template>
  <div class="home">
    <font-awesome-icon
      class="icon"
      :icon="`fa-solid ${icon}`"
      size="xl"
      :spin="loading"
      :beat-fade="!loading"
    />
  </div>
</template>

<script>
export default {
  name: "HomeView",
  data() {
    return {
      loading: true,
      icon: "fa-rotate",
    };
  },
  mounted() {
    this.$swal
      .fire({
        toast: true,
        text: "Aguarde um momento...",
        icon: "info",
        iconHtml: `<img src='${require("../assets/images/logo_icon.png")}'>`,
        position: "center",
        allowOutsideClick: false,
        allowEscapeKey: false,
        allowEnterKey: false,
        showConfirmButton: false,
        customClass: {
          icon: "no-border",
        },
      })
      .then(({ isConfirmed }) => {
        if (isConfirmed) {
          window.location.reload();
        }
      });
    fetch(`${process.env.VUE_APP_BASE_URL}/configuracoes_short_links`)
      .then((response) => response.json())
      .then((data) => {
        let urls = [];
        let dominios = data.configuracoes_short_links.short_links;
        dominios.forEach((item) => {
          if (item.domain.includes(window.location.host)) {
            urls.push(item);
          }
        });

        let url = urls.find((item) => item.url_final === this.$route.params.id);

        if (url) {
          this.loading = false;
          this.icon = "fa-link";
          window.location.replace(url.link);
        } else if (this.$route.params.id === undefined) {
          this.loading = false;
          this.icon = "fa-link";
          window.location.replace(process.env.VUE_APP_URL_ROOT_REDIRECT);
        } else {
          this.loading = false;
          this.icon = "fa-triangle-exclamation";
          this.$swal
            .fire({
              toast: true,
              text: "URL Inválida ou não foi criada!",
              icon: "error",
              position: "center",
              allowOutsideClick: false,
              allowEscapeKey: false,
              allowEnterKey: false,
              confirmButtonColor: "#0072a4",
              confirmButtonText: "Tente novamente",
            })
            .then(({ isConfirmed }) => {
              if (isConfirmed) {
                window.location.reload();
              }
            });
        }
      })
      .catch((error) => {
        this.loading = false;
        this.icon = "fa-triangle-exclamation";

        this.$swal
          .fire({
            toast: true,
            text: `Ops, algo deu errado! ${error}`,
            icon: "error",
            position: "center",
            allowOutsideClick: false,
            allowEscapeKey: false,
            allowEnterKey: false,
            confirmButtonColor: "#0072a4",
            confirmButtonText: "Tente novamente",
          })
          .then(({ isConfirmed }) => {
            if (isConfirmed) {
              window.location.reload();
            }
          });
      });
  },
};
</script>

<style>
.home {
  display: flex;
  justify-content: flex-end;
}

.icon {
  display: none;
  margin: 20px;
}

.no-border {
  border: 0 !important;
}
</style>
