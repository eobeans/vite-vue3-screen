<script setup lang="ts">
import { ref, reactive, nextTick } from "vue";
import { centerMap, GETNOBASE } from "@/api";
import { registerMap, getMap } from "echarts/core";
import { optionHandle, regionCodes } from "./center.map";
import BorderBox13 from "@/components/datav/border-box-13";
import { ElMessage } from "element-plus";

import type { MapdataType } from "./center.map";

import mapData from "./map-data.json";

const option = ref({});
const code = ref("440000"); //china 代表中国 其他地市是行政编码

withDefaults(
  defineProps<{
    // 结束数值
    title: number | string;
  }>(),
  {
    title: "地图",
  }
);

const dataSetHandle = async (regionCode: string, list: object[]) => {
  const geojson: any = await getGeojson(regionCode);
  let cityCenter: any = {};
  let mapData: MapdataType[] = [];
  //获取当前地图每块行政区中心点
  geojson.features.forEach((element: any) => {
    cityCenter[element.properties.name] = element.properties.centroid || element.properties.center;
  });
  //当前中心点如果有此条数据中心点则赋值x，y当然这个x,y也可以后端返回进行大点，前端省去多行代码
  list.forEach((item: any) => {
    if (cityCenter[item.name]) {
      mapData.push({
        name: item.name,
        value: cityCenter[item.name].concat(item.value),
        node: item.children
      });
    } else {
      mapData.push({
        name: item.name,
        value: item.value,
        node: item.children
      });
    }
  });
  await nextTick();

  option.value = optionHandle(regionCode, list, mapData);
};

const getData = async (regionCode: string) => {
  centerMap({ regionCode: regionCode })
    .then((res) => {
      console.log("地图-市场分布", res);
      if (res.success) {
        const marketDataList = mapData["originData"].map((item: any) => {
          return {
            name: item.marketName,
            value: item.value,
            node: item
          }
        })
        console.log("marketDataList", marketDataList)
        let newDataList = res.data.dataList
        if (res.data.regionCode == "china") {
          newDataList = mapData["china"]
        } else if (res.data.regionCode == "440000") {
          newDataList = mapData["440000"]
          // newDataList = [...newDataList, ...marketDataList]
        } else if (res.data.regionCode == "440100") {
          newDataList = [
            {
              "name": "白云区",
              "value": 1000,
              "children": [
                {
                  "area": "白云区",
                  "street": "",
                  "marketName": "罗岗市场",
                  "occupiedArea": "",
                  "rentableArea": "2000",
                  "floorSpace": "",
                  "orderUnit": "刘世良",
                  "orderDate": "2022年1月3日"
                },
                {
                  "area": "白云区",
                  "street": "",
                  "marketName": "棠景市场",
                  "occupiedArea": "",
                  "rentableArea": "2900",
                  "floorSpace": "",
                  "orderUnit": "刘绍星",
                  "orderDate": "2021年2月26日"
                },
              ]
            },
            {
              "name": "番禺区",
              "value": 1000,
              "children": [
                {
                  "area": "番禺区",
                  "street": "",
                  "marketName": "城南市场",
                  "occupiedArea": "",
                  "rentableArea": "2579.33",
                  "floorSpace": "",
                  "orderUnit": "广州市正田市场经营管理有限公司,广州福愉市场经营管理有限公司",
                  "orderDate": "2021年2月26日"
                },
              ]
            },
            {
              "name": "花都区",
              "value": 1000,
              "children": [
                {
                  "area": "花都区",
                  "street": "",
                  "marketName": "芙蓉市场",
                  "occupiedArea": "",
                  "rentableArea": "5824",
                  "floorSpace": "",
                  "orderUnit": "广州市臻阳市场经营管理有限公司",
                  "orderDate": "2021年12月14日"
                },
                {
                  "area": "花都区",
                  "street": "花东镇",
                  "marketName": "象山市场",
                  "occupiedArea": "",
                  "rentableArea": "4500",
                  "floorSpace": "",
                  "orderUnit": "广州市金元物业管理有限公司",
                  "orderDate": "2023年1月29日"
                },
              ]
            },
            {
              "name": "黄埔区",
              "value": 1000,
              "children": [
                {
                  "area": "黄埔区",
                  "street": "",
                  "marketName": "顶岗市场",
                  "occupiedArea": "",
                  "rentableArea": "",
                  "floorSpace": "",
                  "orderUnit": "刘世良、雷霞",
                  "orderDate": "2020年8月1日"
                },
                {
                  "area": "黄埔区",
                  "street": "",
                  "marketName": "港湾北市场",
                  "occupiedArea": "",
                  "rentableArea": "",
                  "floorSpace": "",
                  "orderUnit": "广州市金元物业管理有限公司",
                  "orderDate": "2020年11月17日"
                },
                {
                  "area": "黄埔区",
                  "street": "",
                  "marketName": "联和市场",
                  "occupiedArea": "",
                  "rentableArea": "",
                  "floorSpace": "",
                  "orderUnit": "广州市金元物业管理有限公司",
                  "orderDate": "2009年10月13日"
                },
                {
                  "area": "黄埔区",
                  "street": "",
                  "marketName": "长安市场",
                  "occupiedArea": "",
                  "rentableArea": "",
                  "floorSpace": "",
                  "orderUnit": "广州市金元物业管理有限公司",
                  "orderDate": "2020年11月3日"
                },
              ]
            },
            {
              "name": "荔湾区",
              "value": 1000,
              "children": [
                {
                  "area": "荔湾区",
                  "street": "",
                  "marketName": "克山市场",
                  "occupiedArea": "",
                  "rentableArea": "",
                  "floorSpace": "",
                  "orderUnit": "刘世良",
                  "orderDate": "2021年5月1日"
                },
              ]
            },
            {
              "name": "南沙区",
              "value": 1000,
              "children": [
                {
                  "area": "南沙区",
                  "street": "",
                  "marketName": "板头市场",
                  "occupiedArea": "",
                  "rentableArea": "",
                  "floorSpace": "",
                  "orderUnit": "刘世良",
                  "orderDate": "2022年1月27日"
                },
                {
                  "area": "南沙区",
                  "street": "",
                  "marketName": "金洲市场",
                  "occupiedArea": "",
                  "rentableArea": "",
                  "floorSpace": "",
                  "orderUnit": "广州福愉市场经营管理有限公司",
                  "orderDate": "2021年4月14日"
                },
              ]
            },
            {
              "name": "越秀区",
              "value": 1000,
              "children": [
                {
                  "area": "越秀区",
                  "street": "",
                  "marketName": "建设新村市场",
                  "occupiedArea": "",
                  "rentableArea": "2554",
                  "floorSpace": "",
                  "orderUnit": "广州市金元物业管理有限公司",
                  "orderDate": "2022年12月15日"
                }
              ]
            }
          ]
          newDataList = [...newDataList, ...marketDataList]
        }
        // newDataList = []
        console.log("newDataList", newDataList)
        dataSetHandle(res.data.regionCode, newDataList);
      } else {
        ElMessage.error(res.msg);
      }
    })
    .catch((err) => {
      ElMessage.error(err);
    });
};
const getGeojson = (regionCode: string) => {
  return new Promise<boolean>(async (resolve) => {
    let mapjson = getMap(regionCode);
    if (mapjson) {
      mapjson = mapjson.geoJSON;
      resolve(mapjson);
    } else {
      mapjson = await GETNOBASE(`./map-geojson/${regionCode}.json`).then((data) => data);
      code.value = regionCode;
      registerMap(regionCode, {
        geoJSON: mapjson as any,
        specialAreas: {},
      });
      resolve(mapjson);
    }
  });
};
getData(code.value);

const mapClick = (params: any) => {
  console.log(params.name, regionCodes);
  let xzqData = regionCodes[params.name];
  if (xzqData) {
    getData(xzqData.adcode);
  } else {
    console.log("暂无下级地市")
  }
};
</script>

<template>
  <div class="centermap">
    <div class="maptitle">
      <div class="zuo"></div>
      <span class="titletext">{{ title }}</span>
      <div class="you"></div>
    </div>
    <div class="mapwrap">
      <BorderBox13>
        <div class="quanguo" @click="getData('china')">中国</div>
        <div class="guangdong" @click="getData('440000')">广东</div>
        <v-chart class="chart" :option="option" ref="centerMapRef" @click="mapClick"
          v-if="JSON.stringify(option) != '{}'" />
      </BorderBox13>
    </div>
  </div>
</template>

<style scoped lang="scss">
.centermap {
  margin-bottom: 30px;

  .maptitle {
    height: 60px;
    display: flex;
    justify-content: center;
    padding-top: 10px;
    box-sizing: border-box;

    .titletext {
      font-size: 28px;
      font-weight: 900;
      letter-spacing: 6px;
      background: linear-gradient(92deg, #0072ff 0%, #00eaff 48.8525390625%, #01aaff 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin: 0 10px;
    }

    .zuo,
    .you {
      background-size: 100% 100%;
      width: 29px;
      height: 20px;
      margin-top: 8px;
    }

    .zuo {
      background: url("@/assets/img/xiezuo.png") no-repeat;
    }

    .you {
      background: url("@/assets/img/xieyou.png") no-repeat;
    }
  }

  .mapwrap {
    height: 980px;
    width: 100%;
    // padding: 0 0 10px 0;
    box-sizing: border-box;
    position: relative;

    .quanguo {
      position: absolute;
      right: 20px;
      top: -46px;
      width: 80px;
      height: 28px;
      border: 1px solid #00eded;
      border-radius: 10px;
      color: #00f7f6;
      text-align: center;
      line-height: 26px;
      letter-spacing: 6px;
      cursor: pointer;
      box-shadow: 0 2px 4px rgba(0, 237, 237, 0.5), 0 0 6px rgba(0, 237, 237, 0.4);
      z-index: 10;
    }
  }

  .guangdong {
    position: absolute;
    right: 120px;
    top: -46px;
    width: 80px;
    height: 28px;
    border: 1px solid #00eded;
    border-radius: 10px;
    color: #00f7f6;
    text-align: center;
    line-height: 26px;
    letter-spacing: 6px;
    cursor: pointer;
    box-shadow: 0 2px 4px rgba(0, 237, 237, 0.5), 0 0 6px rgba(0, 237, 237, 0.4);
    z-index: 10;
  }
}
</style>
