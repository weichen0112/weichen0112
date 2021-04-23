<template>
  <div class="mapContainer" ref="map-root"></div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import VectorLayer from "ol/layer/Vector";
import XYZSource from "ol/source/XYZ";
import VectorSource from "ol/source/Vector";
import { getTransform } from "ol/proj";
import { toStringHDMS } from "ol/coordinate";
import { Circle as CircleStyle, Fill, Icon, Stroke, Style } from "ol/style";
import Feature from "ol/Feature";
import Point from "ol/geom/Point";
import Vue from "vue";

import "ol/ol.css";

import storeIconUrl from "./images/store_icon_map.png";

export default Vue.extend({
  data: function () {
    return {
      map: null,
      view: null,
      storeLayer: null,
      storeSource: null,
      xyzLayer: null,
      storeData: [],
    };
  },
  mounted: function () {
    var self = this;

    self.xyzLayer = new TileLayer({
      source: new XYZSource({
        url:
          "https://map.etwarm.com.tw/RiChiMap/getTile.ashx?z={z}&x={x}&y={y}",
      }),
    });

    var storeStyle = new Style({
      image: new Icon({
        src: storeIconUrl,
      }),
    });

    self.storeSource = new VectorSource();

    self.storeLayer = new VectorLayer({
      source: self.storeSource,
    });

    self.storeLayer.setStyle(storeStyle);

    var transform = getTransform("EPSG:4326", "EPSG:3857");
    var coordinate = transform([
      parseFloat("121.51614139999992"),
      parseFloat("24.992428"),
    ]);

    var view = new View({
      center: coordinate,
      zoom: 15,
      minZoom: 10,
      maxZoom: 17,
    });

    self.view = view;

    self.map = new Map({
      target: self.$refs["map-root"],
      layers: [self.xyzLayer, self.storeLayer],
      view: view,
    });

    self.map.on("click", self.mapClick);
    self.map.on("pointermove", self.mapPointerMove);
    self.getStoreData();
    self.renderStore();
  },
  methods: {
    mapClick: function (evt) {
      var self = this;
      var map = this.map;
      var view = this.view;
      var feature = map.forEachFeatureAtPixel(evt.pixel, function (feature) {
        return feature;
      });
      var coordinate, hdms;
      if (feature) {
        coordinate = feature.getGeometry().getCoordinates();
        view.setCenter(coordinate);
        view.setZoom(15);
        self.setFeatureStyle(feature);
      }
    },
    mapPointerMove: function (evt) {
      var map = this.map;
      var pixel = map.getEventPixel(evt.originalEvent);
      var hit = map.hasFeatureAtPixel(pixel);
      map.getTargetElement().style.cursor = hit ? "pointer" : "";
    },
    setFeatureStyle: function (feature) {
      console.log(feature);
      this.storeSource.removeFeature(feature);
      // feature.setStyle(
      //   new Style({
      //     render: function render(coordinates, state) {
      //       var coordinates_0 = coordinates[0];
      //       var x = coordinates_0[0];
      //       var y = coordinates_0[1];
      //       var coordinates_1 = coordinates[1];
      //       var x1 = coordinates_1[0];
      //       var y1 = coordinates_1[1];
      //       var ctx = state.context;
      //       var dx = x1 - x;
      //       var dy = y1 - y;
      //       var radius = Math.sqrt(dx * dx + dy * dy);

      //       var innerRadius = 0;
      //       var outerRadius = radius * 1.4;

      //       var gradient = ctx.createRadialGradient(
      //         x,
      //         y,
      //         innerRadius,
      //         x,
      //         y,
      //         outerRadius
      //       );
      //       gradient.addColorStop(0, "rgba(255,0,0,0)");
      //       gradient.addColorStop(0.6, "rgba(255,0,0,0.2)");
      //       gradient.addColorStop(1, "rgba(255,0,0,0.8)");
      //       ctx.beginPath();
      //       ctx.arc(x, y, radius, 0, 2 * Math.PI, true);
      //       ctx.fillStyle = gradient;
      //       ctx.fill();

      //       ctx.arc(x, y, radius, 0, 2 * Math.PI, true);
      //       ctx.strokeStyle = "rgba(255,0,0,1)";
      //       ctx.stroke();
      //     },
      //   })
      // );
    },
    renderStore: function () {
      var transform = getTransform("EPSG:4326", "EPSG:3857");
      var self = this;
      self.storeData.forEach(function (item) {
        var feature = new Feature(item);
        var coordinate = transform([
          parseFloat(item.lng),
          parseFloat(item.lat),
        ]);
        var geometry = new Point(coordinate);
        feature.setGeometry(geometry);
        self.storeSource.addFeature(feature);
      });
    },
    getStoreData: function () {
      this.storeData = [
        {
          sid: "A1216",
          title: "光復加盟店",
          tel: "02-87895168",
          ads: "台北市信義區光復南路473巷11弄40號1樓",
          zip: "110",
          email: "ta87895168@gmail.com",
          lat: 25.035780230883436,
          lng: 121.55778765678406,
          award: "N",
        },
        {
          sid: "A1266",
          title: "信義101加盟店",
          tel: "02-27204567",
          ads: "台北市信義區忠孝東路5段368號7樓",
          zip: "110",
          email: "et101.aa@msa.hinet.net",
          lat: 25.040717,
          lng: 121.573891,
          award: "N",
        },
        {
          sid: "A0978",
          title: "民權復興加盟店",
          tel: "02-25172788",
          ads: "台北市中山區民權東路三段9號",
          zip: "104",
          email: "aaa0978aaa@gmail.com",
          lat: 25.06253,
          lng: 121.54131,
          award: "N",
        },
        {
          sid: "A1113",
          title: "中山民權捷運加盟店",
          tel: "02-25115600",
          ads: "台北市中山區民權東路1段54號1樓",
          zip: "104",
          email: "et.cm@msa.hinet.net",
          lat: 25.0625154,
          lng: 121.5245281,
          award: "N",
        },
        {
          sid: "A0518",
          title: "北投中央加盟店",
          tel: "02-28936799",
          ads: "台北市北投區中央南路一段100號",
          zip: "112",
          email: "a0518@etrealty.com.tw",
          lat: 25.135065,
          lng: 121.49934800000005,
          award: "N",
        },
        {
          sid: "A0119",
          title: "內湖加盟店",
          tel: "02-26588077",
          ads: "台北市內湖區文德路217號4樓",
          zip: "114",
          email: "a0119@etrealty.com.tw",
          lat: 25.0792543,
          lng: 121.57778289999999,
          award: "N",
        },
        {
          sid: "A1294",
          title: "萬華加盟店",
          tel: "02-23321991",
          ads: "台北市萬華區萬大路148號",
          zip: "108",
          email: "live.large@msa.hinet.net",
          lat: 25.0288154,
          lng: 121.50031650000005,
          award: "N",
        },
        {
          sid: "A0018",
          title: "中山捷運加盟店",
          tel: "02-25550088",
          ads: "台北市大同區承德路二段18號",
          zip: "103",
          email: "rbut0043@yahoo.com.tw",
          lat: 25.055468,
          lng: 121.51795600000003,
          award: "N",
        },
        {
          sid: "A1074",
          title: "北投奇岩捷運加盟店",
          tel: "02-28967788",
          ads: "台北市北投區三合街二段480號",
          zip: "112",
          email: "shuyeah22@gmail.com",
          lat: 25.126269980512053,
          lng: 121.50107359391791,
          award: "N",
        },
        {
          sid: "A0464",
          title: "台北松高加盟店",
          tel: "02-87807759",
          ads: "台北市信義區忠孝東路5段236巷30號",
          zip: "110",
          email: "hungkuang88@gmail.com",
          lat: 25.039503276563746,
          lng: 121.57180511350919,
          award: "N",
        },
        {
          sid: "A1334",
          title: "北投重劃區加盟店",
          tel: "02-28955666",
          ads: "台北市北投區中央北路一段96號1樓",
          zip: "112",
          email: "fubon02@gmail.com",
          lat: 25.1347801,
          lng: 121.4997934,
          award: "N",
        },
        {
          sid: "A0591",
          title: "南京小巨蛋加盟店",
          tel: "02-25788811",
          ads: "台北市松山區南京東路4段50號12樓",
          zip: "105",
          email: "kch.mail88@gmail.com",
          lat: 25.048153,
          lng: 121.552893,
          award: "N",
        },
        {
          sid: "A0279",
          title: "南門加盟店",
          tel: "02-23213388",
          ads: "台北市中正區南昌路一段75號",
          zip: "100",
          email: "God.withyou@msa.hinet.net",
          lat: 25.03089429410273,
          lng: 121.517541046627,
          award: "N",
        },
        {
          sid: "A0764",
          title: "木新加盟店",
          tel: "02-29383000",
          ads: "台北市文山區木新路3段101號1樓",
          zip: "116",
          email: "cyc29383000@yahoo.com.tw",
          lat: 24.982046,
          lng: 121.56269799999995,
          award: "N",
        },
        {
          sid: "A0535",
          title: "敦北加盟店",
          tel: "02-87127533",
          ads: "台北市松山區敦化北路155巷15號",
          zip: "105",
          email: "chief.roc@msa.hinet.net",
          lat: 25.054151,
          lng: 121.550735,
          award: "N",
        },
        {
          sid: "A1182",
          title: "忠孝SOGO加盟店",
          tel: "02-27760555",
          ads: "台北市大安區忠孝東路3段217巷3弄10號1樓",
          zip: "106",
          email: "victory27760555@gmail.com",
          lat: 25.042966,
          lng: 121.539188,
          award: "N",
        },
        {
          sid: "A0860",
          title: "明德捷運加盟店",
          tel: "02-28286555",
          ads: "台北市北投區明德路201之2號1樓",
          zip: "112",
          email: "ok28286555ok@yahoo.com.tw",
          lat: 25.109659,
          lng: 121.51961600000004,
          award: "N",
        },
        {
          sid: "A0504",
          title: "景美加盟店",
          tel: "02-29357777",
          ads: "台北市文山區景中街23號",
          zip: "116",
          email: "rb.cs7777@msa.hinet.net",
          lat: 24.9930449,
          lng: 121.54246899999998,
          award: "N",
        },
        {
          sid: "A0102",
          title: "木柵加盟店",
          tel: "02-29390746",
          ads: "台北市文山區木柵路二段115號1樓",
          zip: "116",
          email: "w29390746@gmail.com",
          lat: 24.989,
          lng: 121.56193699999994,
          award: "N",
        },
        {
          sid: "A0002",
          title: "會員服務中心",
          tel: "02-23896885",
          ads: "台北市中正區館前路42號4樓",
          zip: "100",
          email: "0800@etwarm.com.tw",
          lat: 24.992428,
          lng: 121.51614139999992,
          award: "N",
        },
        {
          sid: "A1132",
          title: "北投捷運加盟店",
          tel: "02-28985858",
          ads: "台北市北投區光明路47號1樓",
          zip: "112",
          email: "h58tw@yahoo.com.tw",
          lat: 25.132843,
          lng: 121.49964299999999,
          award: "N",
        },
        {
          sid: "A0399",
          title: "南京復興加盟店",
          tel: "02-27814000",
          ads: "台北市中山區復興北路92號6樓",
          zip: "104",
          email: "er2781@yahoo.com.tw",
          lat: 25.051184,
          lng: 121.54373,
          award: "N",
        },
        {
          sid: "A0156",
          title: "內湖捷運加盟店",
          tel: "02-27962900",
          ads: "台北市內湖區成功路四段211號1樓",
          zip: "114",
          email: "house27962900@yahoo.com.tw",
          lat: 25.0842506,
          lng: 121.596236,
          award: "N",
        },
        {
          sid: "A0063",
          title: "仁愛加盟店",
          tel: "02-27218988",
          ads: "台北市大安區仁愛路四段392號1樓",
          zip: "106",
          email: "www.88house@hotmail.com",
          lat: 25.0373279,
          lng: 121.55658790000007,
          award: "N",
        },
      ];
    },
  },
});
</script>

<style>
.mapContainer {
  width: 100%;
  height: 800px;
}
</style>

