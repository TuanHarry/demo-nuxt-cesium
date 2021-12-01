<template>
	<div class="w-full h-full relative">
		<vc-viewer
			:animation="animation"
			:timeline="timeline"
			:camera.sync="camera"
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
			timeline: true,
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

			dataAreaVN: areaVN,
			entities: [],
			// Control
			showPolygon: false,
			showGraphics: false,
		};
	},
	methods: {
		ready(cesiumInstance) {
			const { Cesium, viewer } = cesiumInstance;
			// Get Cesium and viewer instances here, then execute the relevant logic code
			this.camera.position.lng = 106.6178;
			this.camera.position.lat = 10.8;
			this.camera.position.height = 5000;
			this.animation = true;

			// Get the array of entities

			this.dataAreaVN.features.forEach((entity) => {
				console.debug("entity", entity);
				this.entities.push({
					fill: entity.properties.fill_color,
				});
			});
		},

		subReady(cesiumInstance) {
			cesiumInstance.viewer.zoomTo(cesiumInstance.cesiumObject);
		},

		// Action change polygon
		change(val, id) {
			console.debug("change", id);
			if ("polygon_basic_button" === id) {
				this.showPolygon = val;
			} else if ("polygon_graphics_button" === id) {
				this.showGraphics = val;
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