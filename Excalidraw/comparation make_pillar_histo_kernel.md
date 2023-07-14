---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
__global__ void make_pillar_histo_kernel(
    const float* dev_points, 
    float* dev_pillar_x_in_coors,
    float* dev_pillar_y_in_coors, 
    float* dev_pillar_z_in_coors,
    float* dev_pillar_i_in_coors, 
    int* pillar_count_histo, 
    const int num_points,
    const int max_points_per_pillar, 
    const int GRID_X_SIZE,
    const int GRID_Y_SIZE, 
    const int GRID_Z_SIZE, 
    const float MIN_X_RANGE,
    const float MIN_Y_RANGE, 
    const float MIN_Z_RANGE, 
    const float PILLAR_X_SIZE,
    const float PILLAR_Y_SIZE, 
    const float PILLAR_Z_SIZE,
    const int NUM_BOX_CORNERS) ^VDLe2SoL

make_pillar_histo_kernel<<<num_block, NUM_THREADS_>>>(
    dev_points, 点云
    dev_pillar_x_in_coors_, 
    dev_pillar_y_in_coors_,
    dev_pillar_z_in_coors_, 
    dev_pillar_i_in_coors_, 
    dev_pillar_count_histo_,
    in_num_points, 
    MAX_NUM_POINTS_PER_PILLAR_, 
    GRID_X_SIZE_, 
    GRID_Y_SIZE_,
    GRID_Z_SIZE_, 
    MIN_X_RANGE_, 
    MIN_Y_RANGE_, 
    MIN_Z_RANGE_, 
    PILLAR_X_SIZE_,
    PILLAR_Y_SIZE_, 
    PILLAR_Z_SIZE_, 
    NUM_BOX_CORNERS_); ^ZNI98sqd

%%
# Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/1.9.6",
	"elements": [
		{
			"type": "text",
			"version": 443,
			"versionNonce": 1061448371,
			"isDeleted": false,
			"id": "VDLe2SoL",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -218.9591737512892,
			"y": -248.4966659594411,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 384.375,
			"height": 364.8,
			"seed": 908761715,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1689125489398,
			"link": null,
			"locked": false,
			"fontSize": 16,
			"fontFamily": 3,
			"text": "__global__ void make_pillar_histo_kernel(\n    const float* dev_points, \n    float* dev_pillar_x_in_coors,\n    float* dev_pillar_y_in_coors, \n    float* dev_pillar_z_in_coors,\n    float* dev_pillar_i_in_coors, \n    int* pillar_count_histo, \n    const int num_points,\n    const int max_points_per_pillar, \n    const int GRID_X_SIZE,\n    const int GRID_Y_SIZE, \n    const int GRID_Z_SIZE, \n    const float MIN_X_RANGE,\n    const float MIN_Y_RANGE, \n    const float MIN_Z_RANGE, \n    const float PILLAR_X_SIZE,\n    const float PILLAR_Y_SIZE, \n    const float PILLAR_Z_SIZE,\n    const int NUM_BOX_CORNERS)",
			"rawText": "__global__ void make_pillar_histo_kernel(\n    const float* dev_points, \n    float* dev_pillar_x_in_coors,\n    float* dev_pillar_y_in_coors, \n    float* dev_pillar_z_in_coors,\n    float* dev_pillar_i_in_coors, \n    int* pillar_count_histo, \n    const int num_points,\n    const int max_points_per_pillar, \n    const int GRID_X_SIZE,\n    const int GRID_Y_SIZE, \n    const int GRID_Z_SIZE, \n    const float MIN_X_RANGE,\n    const float MIN_Y_RANGE, \n    const float MIN_Z_RANGE, \n    const float PILLAR_X_SIZE,\n    const float PILLAR_Y_SIZE, \n    const float PILLAR_Z_SIZE,\n    const int NUM_BOX_CORNERS)",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "__global__ void make_pillar_histo_kernel(\n    const float* dev_points, \n    float* dev_pillar_x_in_coors,\n    float* dev_pillar_y_in_coors, \n    float* dev_pillar_z_in_coors,\n    float* dev_pillar_i_in_coors, \n    int* pillar_count_histo, \n    const int num_points,\n    const int max_points_per_pillar, \n    const int GRID_X_SIZE,\n    const int GRID_Y_SIZE, \n    const int GRID_Z_SIZE, \n    const float MIN_X_RANGE,\n    const float MIN_Y_RANGE, \n    const float MIN_Z_RANGE, \n    const float PILLAR_X_SIZE,\n    const float PILLAR_Y_SIZE, \n    const float PILLAR_Z_SIZE,\n    const int NUM_BOX_CORNERS)",
			"lineHeight": 1.2,
			"baseline": 361
		},
		{
			"type": "text",
			"version": 517,
			"versionNonce": 1549590675,
			"isDeleted": false,
			"id": "ZNI98sqd",
			"fillStyle": "hachure",
			"strokeWidth": 1,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -752.1309682853343,
			"y": -250.05601634555381,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 506.25,
			"height": 364.8,
			"seed": 1553201629,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1689126299122,
			"link": null,
			"locked": false,
			"fontSize": 16,
			"fontFamily": 3,
			"text": "make_pillar_histo_kernel<<<num_block, NUM_THREADS_>>>(\n    dev_points, 点云\n    dev_pillar_x_in_coors_, \n    dev_pillar_y_in_coors_,\n    dev_pillar_z_in_coors_, \n    dev_pillar_i_in_coors_, \n    dev_pillar_count_histo_,\n    in_num_points, \n    MAX_NUM_POINTS_PER_PILLAR_, \n    GRID_X_SIZE_, \n    GRID_Y_SIZE_,\n    GRID_Z_SIZE_, \n    MIN_X_RANGE_, \n    MIN_Y_RANGE_, \n    MIN_Z_RANGE_, \n    PILLAR_X_SIZE_,\n    PILLAR_Y_SIZE_, \n    PILLAR_Z_SIZE_, \n    NUM_BOX_CORNERS_);",
			"rawText": "make_pillar_histo_kernel<<<num_block, NUM_THREADS_>>>(\n    dev_points, 点云\n    dev_pillar_x_in_coors_, \n    dev_pillar_y_in_coors_,\n    dev_pillar_z_in_coors_, \n    dev_pillar_i_in_coors_, \n    dev_pillar_count_histo_,\n    in_num_points, \n    MAX_NUM_POINTS_PER_PILLAR_, \n    GRID_X_SIZE_, \n    GRID_Y_SIZE_,\n    GRID_Z_SIZE_, \n    MIN_X_RANGE_, \n    MIN_Y_RANGE_, \n    MIN_Z_RANGE_, \n    PILLAR_X_SIZE_,\n    PILLAR_Y_SIZE_, \n    PILLAR_Z_SIZE_, \n    NUM_BOX_CORNERS_);",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "make_pillar_histo_kernel<<<num_block, NUM_THREADS_>>>(\n    dev_points, 点云\n    dev_pillar_x_in_coors_, \n    dev_pillar_y_in_coors_,\n    dev_pillar_z_in_coors_, \n    dev_pillar_i_in_coors_, \n    dev_pillar_count_histo_,\n    in_num_points, \n    MAX_NUM_POINTS_PER_PILLAR_, \n    GRID_X_SIZE_, \n    GRID_Y_SIZE_,\n    GRID_Z_SIZE_, \n    MIN_X_RANGE_, \n    MIN_Y_RANGE_, \n    MIN_Z_RANGE_, \n    PILLAR_X_SIZE_,\n    PILLAR_Y_SIZE_, \n    PILLAR_Z_SIZE_, \n    NUM_BOX_CORNERS_);",
			"lineHeight": 1.2,
			"baseline": 361
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#1e1e1e",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "hachure",
		"currentItemStrokeWidth": 1,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 3,
		"currentItemFontSize": 16,
		"currentItemTextAlign": "left",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"scrollX": 791.9289995897402,
		"scrollY": 685.9165197239261,
		"zoom": {
			"value": 0.7000000000000001
		},
		"currentItemRoundness": "round",
		"gridSize": null,
		"currentStrokeOptions": null,
		"previousGridSize": null
	},
	"files": {}
}
```
%%