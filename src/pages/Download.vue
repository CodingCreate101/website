<template>
	<Layout>
		<div class="download">
			<container>
				<div class="download__container">
					<div v-if="!!platformName" class="download__progress">
						<h2>Thanks for downloading Thermal for {{ platformName }}</h2>
						<p>
							Download not started? Try this
							<a :href="downloadLink" target="_blank">direct download link</a>.
						</p>
					</div>
					<h1 v-else class="download__heading">
						Thermal for Windows, Mac & Linux
					</h1>
					<div class="download__other">
						<p class="download__other-heading" v-if="!!platformName">
							Download for other platform
						</p>
						<div class="download__other-list">
							<div class="download__other-item">
								<g-image
									src="../../static/images/icon/windows.svg"
									class="download__other-image"
								/>
								<h4>Windows</h4>
								<div>
									<select v-model="winBuild" name="download__os-win">
										<option value="exe">Exe</option>
									</select>
									<outline-button
										text="Download"
										:link="downloadBuild(0, winBuild)"
										:size="1"
										theme="dark"
										:external="true"
									/>
								</div>
							</div>
							<div class="download__other-item">
								<g-image
									src="../../static/images/icon/mac.png"
									class="download__other-image"
								/>
								<h4>MacOS</h4>
								<div>
									<select v-model="macBuild" name="download__os-mac">
										<option value="dmg">Dmg</option>
										<option value="zip">Zip</option>
									</select>
									<outline-button
										text="Download"
										:link="downloadBuild(1, macBuild)"
										:size="1"
										theme="dark"
										:external="true"
									/>
								</div>
							</div>
							<div class="download__other-item">
								<g-image
									src="../../static/images/icon/linux.svg"
									class="download__other-image"
								/>
								<h4>Linux</h4>
								<div>
									<select v-model="linuxBuild" name="download__os-linux">
										<option value="deb">Deb</option>
										<option value="snap">Snap</option>
										<option value="zip">Zip</option>
										<option value="AppImage">AppImage</option>
									</select>
									<outline-button
										text="Download"
										:link="downloadBuild(2, linuxBuild)"
										:size="1"
										theme="dark"
										:external="true"
									/>
								</div>
							</div>
						</div>
					</div>
				</div>
			</container>
		</div>
	</Layout>
</template>

<script>
import container from "../layouts/Container";
import PlatformMixin from "../mixins/platform";
import OutlineButton from "../components/Button/OutlineButton";

export default {
	name: "Download",
	metaInfo: {
		title: "Download windows, mac & linux",
		link: [
			{
				rel: "canonical",
				href: "https://thermal.codecarrot.net/download"
			}
		]
	},
	data() {
		return {
			currentOSDownloadURL: "",
			winBuild: "",
			linuxBuild: "",
			macBuild: "",
			osBuild: [
				{
					os: "windows",
					ext: []
				},
				{
					os: "mac",
					ext: []
				},
				{
					os: "linux",
					ext: []
				}
			]
		};
	},
	components: {
		container,
		OutlineButton
	},
	computed: {
		downloadLink() {
			return this.currentOSDownloadURL;
		}
	},
	methods: {
		osDownloadRedirect(url) {
			setTimeout(function() {
				window.open(url);
			}, 1500);
		},
		osReleasesAssets(assets) {
			for (let i = 0; i < assets.length; i++) {
				let downloadUrl = assets[i].browser_download_url;
				if (downloadUrl.includes("linux")) {
					if (downloadUrl.includes("deb")) {
						this.addBuildType(2, "deb", downloadUrl);
					}
					if (downloadUrl.includes("snap")) {
						this.addBuildType(2, "snap", downloadUrl);
					}
					if (downloadUrl.includes("zip")) {
						this.addBuildType(2, "zip", downloadUrl);
					}
					if (downloadUrl.includes("AppImage")) {
						this.addBuildType(2, "AppImage", downloadUrl);
					}
				}
				if (downloadUrl.includes("mac")) {
					if (downloadUrl.includes("dmg")) {
						this.addBuildType(1, "dmg", downloadUrl);
					}
					if (downloadUrl.includes("zip")) {
						this.addBuildType(1, "zip", downloadUrl);
					}
				}
				if (downloadUrl.includes("win")) {
					this.addBuildType(0, "exe", downloadUrl);
				}
			}
		},
		addBuildType(index, type, downloadUrl) {
			let data = {
				name: type,
				browser_download_url: downloadUrl
			};
			this.osBuild[index].ext.push(data);
		},
		downloadBuild(index, build) {
			for (let i = 0; i < this.osBuild[index].ext.length; i++) {
				if (this.osBuild[index].ext[i].name === build) {
					return this.osBuild[index].ext[i].browser_download_url;
				}
			}
		}
	},
	mounted() {
		this.osReleasesAssets(this.$page.allgithub.edges[0].node.assets);
		for (let i = 0; i < this.osBuild.length; i++) {
			if (this.$router.history.current.query.os === this.osBuild[i].os) {
				for (let j = 0; j < this.osBuild[i].ext.length; j++) {
					this.currentOSDownloadURL = this.osBuild[i].ext[
						j
					].browser_download_url;
					this.osDownloadRedirect(this.currentOSDownloadURL);
				}
			}
		}
	},
	mixins: [PlatformMixin]
};
</script>

<page-query>
	query GitHub {
		allgithub (sort: [{ by: "published_at" }]) {
			edges {
				node {
					id
					name
					assets {
						url
						browser_download_url
					}
				}
			}
		}
	}
</page-query>

<style lang="sass">
.download
	padding-top: 60px
	padding-bottom: 60px
	display: flex
	text-align: center

	&__heading
		font-size: 2.5rem
		margin-bottom: 2rem

	&__progress
		background-color: #EFEFEF
		color: #7A7D84
		padding: 1.2rem 1.5rem
		border-radius: .3rem
		margin-bottom: 3rem

		a
			color: #00ADB5

	&__other

		&-heading
			margin-bottom: 1rem

		&-list
			display: flex
			flex-direction: column

		&-item
			display: flex
			flex-direction: column
			box-shadow: rgba(15, 15, 15, 0.15) 0px 0px 2.5px 1px
			align-items: center
			border-radius: .3rem
			padding: 1rem
			margin-bottom: 1rem
			color: black

			&:hover
				box-shadow: rgba(15, 15, 15, 0.15) 0px 0px 16px 0px

		&-image
			width: 50px
			height: 50px
			margin-bottom: .875rem

@media (min-width: 768px)
	.download
		&__other
			&-list
				flex-direction: row
				justify-content: space-evenly

			&-item
				padding: 3rem 4rem

</style>
