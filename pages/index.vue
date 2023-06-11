<template>
  <div class="page">
    <div class="intro">
      <div class="intro__container">
        <nav class="nav">
          <a href="#screenings" class="cursor-pointer">Screenings</a>
          <a href="#about">About</a>
          <nuxt-link to="/map">Explore Lofoten in VR</nuxt-link>
        </nav>

        <video autoplay muted loop playsinline width="100%" class="video">
          <source :src="require(`~/assets/video/til.mp4`)" type="video/mp4" />
          <source :src="require(`~/assets/video/til.webm`)" type="video/webm" />
        </video>

        <div class="content">
          <div
            class="tikkio-widget-events"
            w-id="11905"
            w-token="388df71fb520de335e87983437ef92a0011eff42"
            w-target="blank"
          ></div>
          <script src="https://a.tikkio.com/static/1.0.1531/js/widgets.js"></script>
        </div>

        <div class="content text-center " id="screenings">
          <h2 class="">Screenings every day</h2>
          <ul>
            <li>14:00</li>
            <li>15:00</li>
            <li>16:00</li>
            <li>17:00</li>
            <li>18:00</li>
            <li>19:00 (Southbound Hurtigrute)</li>
            <li>20:00 (Southbound Hurtigrute)</li>
            <li>21:00</li>
            <li>21:35 (Northbound Hurtigrute)</li>
          </ul>
          <span>Tickets are versatile and valid for any screening</span>
          <img
            :src="require('~/assets/img/contours.svg')"
            class="contours-bg pointer-events-none"
          />
        </div>
        <div class="content">
          <iframe
            src="https://www.google.com/maps/embed?pb=!1m14!1m8!1m3!1d739.8432447394639!2d14.5643064!3d68.2309871!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x45de8079ebd92b31%3A0xffa4b119f354112b!2sLofoten%20kulturhus!5e0!3m2!1sno!2sno!4v1686475447993!5m2!1sno!2sno"
            width="100%"
            height="350"
            style="border:0;"
            allowfullscreen=""
            loading="lazy"
            referrerpolicy="no-referrer-when-downgrade"
          ></iframe>
        </div>
        <div class="content  text-center" id="about">
          <h2>About</h2>
          <p>
            Experience the wonders of Lofoten's four seasons in "This is
            Lofoten," a captivating 20-minute cinematic journey.
          </p>
          <p>
            Witness the mesmerizing northern lights, intense storms, and serene
            polar nights. Bask in the radiant glow of the midnight sun,
            surrounded by the majestic sea and vibrant wildlife. Explore the
            dramatic archipelago's diverse shapes and colors, capturing the
            essence of Lofoten's extraordinary nature.
          </p>
          <img
            :src="require('~/assets/img/contours.svg')"
            class="contours-bg pointer-events-none"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  head() {
    return {
      title: this.initialOpen
        ? this.initialDrawerContent.title.replace(/\b\w/g, (l) =>
            l.toUpperCase(),
          )
        : 'This Is Lofoten',
      meta: [
        // hid is used as unique identifier. Do not use `vmid` for it as it will not work
        {
          hid: 'description',
          name: 'description',
          content:
            'Immerse yourself in the beauty of the Lofoten Islands in 360° virtual reality. Discover the area through the map and choose your favourite place, activity or season.',
        },
        {
          hid: 'og:title',
          property: 'og:title',
          content: 'This Is Lofoten',
        },
        {
          hid: 'og:description',
          property: 'og:description',
          content:
            'Immerse yourself in the beauty of the Lofoten Islands in 360° virtual reality. Discover the area through the map and choose your favourite place, activity or season.',
        },
        {
          hid: 'og:url',
          property: 'og:url',
          content: `https://thisislofoten.com`,
        },
        {
          hid: 'og:image',
          property: 'og:image',
          content: 'https://thisislofoten.com/social.jpg',
        },
      ],
    }
  },
}
</script>

<style scoped>
.page {
  width: 100%;
  height: 100%;
  min-height: 100vh;
  min-width: 100vw;
  background: url('~assets/img/til.jpeg');
  background-size: cover;
  background-attachment: fixed;
}
.contours-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 0;
  transform: rotate(180deg);
  opacity: 0.75;
}
.intro {
  width: 100%;
  position: relative;
  top: 0;
  left: 0;
  backdrop-filter: blur(10px);
  padding: 0.5rem 0;
}

@media screen and (min-width: 900px) {
  .intro {
    padding: 3rem 0 5rem;
  }
}

.intro__container {
  width: 80%;
  margin: 0 auto;
}

@media screen and (min-width: 900px) {
  .intro__container {
    width: 900px;
    max-width: 50%;
  }
}

.nav {
  margin: 2rem 0 1rem;
  padding: 1rem;
  background: rgba(255, 255, 255, 0.4);
  border-radius: 0.5rem;
  color: #fff;
  text-transform: uppercase;
  font-weight: 700;
  letter-spacing: 0.1rem;
  font-size: 0.75rem;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  text-align: center;
}
.nav a {
  padding: 0.5rem;
}

@media screen and (min-width: 600px) {
  .nav {
    flex-direction: row;
  }
  .nav a {
    padding: 0rem;
  }
}

.video {
  border-radius: 0.5rem;
}

.content {
  margin-top: 1rem;
  padding: 1rem;
  width: 100%;
  background: rgba(255, 255, 255, 0.4);
  border-radius: 0.5rem;
  position: relative;
  overflow: hidden;
  backdrop-filter: blur(30px);
}

h2 {
  font-size: 1rem;
  margin: 1rem 0;
  text-transform: uppercase;
  font-weight: 700;
  letter-spacing: 0.1rem;
  color: #222;
}

@media screen and (min-width: 600px) {
  .h2 {
    margin: 2rem 0;
  }
}

ul {
  margin-bottom: 2rem;
  font-weight: 500;
}
span {
  display: block;
  margin-bottom: 2rem;
  font-style: italic;
  font-size: 0.8rem;
}
iframe {
  border: none;
  border-radius: 0.5rem;
}

p {
  padding: 0 1rem 2rem;
  font-weight: 500;
  text-align: left;
  font-size: 0.8rem;
}

@media screen and (min-width: 600px) {
  p {
    padding: 0 2rem 2rem;
    font-size: 1.1rem;
  }
}
</style>
