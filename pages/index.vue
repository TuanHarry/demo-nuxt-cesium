<template>
	<div class="w-full h-full relative">
		<vc-viewer
			:animation="animation"
			:camera.sync="camera"
			:fullscreenButton="true"
			@ready="ready"
		>
			<!-- nav control  -->
			<vc-navigation
				ref="navigation"
				:options="options"
			></vc-navigation>

			<!-- layer  -->
			<vc-layer-imagery>
				<vc-provider-imagery-bingmaps
					url="https://dev.virtualearth.net"
					bmKey="AgcbDCAOb9zMfquaT4Z-MdHX4AsHUNvs7xgdHefEA5myMHxZk87NTNgdLbG90IE-"
					mapStyle="AerialWithLabels"
				></vc-provider-imagery-bingmaps>
			</vc-layer-imagery>

			<!-- Draw polygon -->
			<vc-datasource-geojson
				v-if="showPolygon"
				ref="ds"
				:show="showPolygon"
				:options="options"
				:data="dataAreaVN"
			></vc-datasource-geojson>

			<!-- Draw graphic -->
			<vc-datasource-geojson
				v-if="showGraphics"
				ref="ds1"
				@ready="subReady"
				:show="showGraphics"
				:options="options"
				:entities="entities"
			></vc-datasource-geojson>

			<vc-entity
				:position="position1"
				:point.sync="point1"
			>
				<vc-graphics-point
					ref="point1"
					:color="color1"
					:pixelSize="8"
				></vc-graphics-point>
			</vc-entity>

			<vc-collection-primitive :show="true">
				<vc-primitive-model
					ref="model"
					:url="url"
					:modelMatrix="modelMatrix"
					:scale="10000"
					:minimumPixelSize="128"
					:maximumScale="200000"
				></vc-primitive-model>
			</vc-collection-primitive>
		</vc-viewer>

		<!-- option control  -->
		<div class="absolute top-3 left-3 bg-white border-gray-300 rounded p-3 flex flex-col">
			<div class="flex flex-row items-center">
				<toggle-button
					ref="toggle_polygon"
					:id="'polygon_basic'"
					:labelEnableText="''"
					:labelDisableText="''"
					:defaultState="showPolygon"
					@change="change"
				></toggle-button>
				<span class="ml-2 font-bold text-gray-600">Polygon</span>
			</div>
			<div class="flex flex-row items-center">
				<toggle-button
					ref="toggle_graphics"
					:id="'polygon_graphics'"
					:labelEnableText="''"
					:labelDisableText="''"
					:defaultState="showGraphics"
					@change="change"
				></toggle-button>
				<span class="ml-2 font-bold text-gray-600">Graphics</span>
			</div>
			<div class="flex flex-row items-center">
				<toggle-button
					ref="toggle_box"
					:id="'box'"
					:labelEnableText="''"
					:labelDisableText="''"
					:defaultState="showBox"
					@change="change"
				></toggle-button>
				<span class="ml-2 font-bold text-gray-600">Box</span>
			</div>
		</div>

	</div>
</template>
<script>
import ToggleButton from "../components/ToggleButton.vue";
import "vue-cesium/lib/vc-navigation.css";
import areaVN from "~/static/AreaVN";
export default {
	components: { ToggleButton },
	data() {
		return {
			animation: true,
			camera: {
				position: {
					lng: 106.6178,
					lat: 10.8,
					height: 100000,
				},
				heading: 360,
				pitch: -65,
				roll: 0,
			},
			options: {
				enableCompass: true,
				enableZoomControl: true,
				enableDistanceLegend: true,
				enableLocationBar: true,
				enableCompassOuterRing: true,
				enablePrintView: true,
				enableMyLocation: true,
				stroke: "red",
			},
			cesiumGlobal: null,

			dataAreaVN: areaVN,
			entities: [],

			// Point
			point1: null,
			color1: {},
			position1: { lng: 106.6178, lat: 10.8 },

			// Model
			modelMatrix: {},
			url: "~/static/Duck.gltf",

			// Control
			showPolygon: false,
			showGraphics: false,
			showBox: true,
		};
	},

	mounted() {
		Promise.all([this.$refs.point1.createPromise]).then((instances) => {
			instances[0].viewer.zoomTo(instances[0].viewer.entities);
		});
	},

	methods: {
		ready(cesiumInstance) {
			const { Cesium, viewer } = cesiumInstance;
			this.cesiumGlobal = cesiumInstance;
			// Get Cesium and viewer instances here, then execute the relevant logic code
			this.camera.position.lng = 106.6178;
			this.camera.position.lat = 10.8;
			this.camera.position.height = 5000;
			this.animation = true;

			this.color1 = Cesium.Color.RED;

			// viewer.entities.add({
			// 	name: "Blue box",
			// 	position: Cesium.Cartesian3.fromDegrees(106.6178, 10.8, 0.0),
			// 	box: {
			// 		dimensions: new Cesium.Cartesian3(40.0, 30.0, 200.0),
			// 		material: Cesium.Color.BLUE,
			// 	},
			// });

			this.addBox();

			this.modelMatrix = Cesium.Transforms.eastNorthUpToFixedFrame(
				Cesium.Cartesian3.fromDegrees(106.6178, 10.8, 100)
			);
		},

		subReady(cesiumInstance) {
			cesiumInstance.viewer.zoomTo(cesiumInstance.cesiumObject);
		},

		addBox() {
			console.debug(this.cesiumGlobal);
			if (this.cesiumGlobal) {
				this.cesiumGlobal.viewer.entities.add({
					name: "Blue box",
					position: Cesium.Cartesian3.fromDegrees(106.6178, 10.8, 0.0),
					box: {
						dimensions: new Cesium.Cartesian3(40.0, 30.0, 200.0),
						material: Cesium.Color.BLUE,
					},
				});
			}
		},

		// Action change polygon
		change(val, id) {
			console.debug("change", id);
			if ("polygon_basic_button" === id) {
				this.showPolygon = val;
			} else if ("polygon_graphics_button" === id) {
				this.showGraphics = val;
			} else if ("box_button" === id) {
				this.showBox = val;
			}
		},
	},
};
</script>
<style>
.viewer {
	width: 100%;
	height: 400px;
}
</style>