<script lang="ts">
	import { T } from '@threlte/core';
	import { GLTF, OrbitControls, useGltf, useSuspense } from '@threlte/extras';
	import { RGBAFormat } from 'three';

	const suspend = useSuspense();

	const gltf = suspend(
		useGltf('./printed-voron-stealthburner-body-textured.glb', { useDraco: true })
	);

	$: model = $gltf?.scene.children[0];
</script>

<T.AmbientLight />
<T.DirectionalLight position={[3, 10, 7]} />
<T.PerspectiveCamera position={[-10, 25, 0]} makeDefault fov={50}>
	<OrbitControls enableDamping />
</T.PerspectiveCamera>

<!-- This one works correctly, left in the scene -->
<GLTF scale={0.1} url="./printed-voron-stealthburner-body-textured.glb" useDraco />

<!-- This doesn't work right, the normals look flipped. Also, if you could help me figure out the AO map, that would be great -->
<T.Group scale={0.1} position={[7, 0, 0]} dispose={false}>
	{#if model}
		<T.Mesh castShadow receiveShadow>
			<T is={model.geometry} />
			<T.MeshStandardMaterial roughness={0.3} color="#bb2222" metalness={0} aoMapIntensity={0.5}>
				<T
					flipY
					format={RGBAFormat}
					colorSpace="srgb-linear"
					mapping={301}
					type={1009}
					is={model.material.normalMap}
					attach="normalMap"
				/>
				<T format={RGBAFormat} is={model.material.map} attach="aoMap" />
			</T.MeshStandardMaterial>
		</T.Mesh>
	{/if}
</T.Group>
