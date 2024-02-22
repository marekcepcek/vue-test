<script lang="ts">
import { defineComponent, PropType } from 'vue';

export default defineComponent({
	name: 'ProductTile',
	props: {
		product: {
			type: Object as PropType<{
				id: number;
				title: string;
				price: number;
				description: string;
				category: string;
				image: string;
				rating: {
					rate: number;
					count: number;
				};
			}>,
			required: true,
		},
	},
	setup: (props) => {
		const croppedDescription = props.product.description.slice(0, 80) + '...';

		return {
			croppedDescription,
		};
	},
});

</script>

<template>
	<li class="product">
		<a href="">
			<div class="product__image">
				<figure>
					<img :src="product.image" :alt="product.title" loading="lazy" />
				</figure>
			</div>
			<div class="product__info">
				<span class="product__info__category">{{ product.category }}</span>
				<h2 class="product__info__title">{{ product.title }}</h2>
				<p class="product__info__description">{{ croppedDescription }}</p>
				<div class="product__info__wrap">
					<p class="product__info__wrap-price">${{ product.price }}</p>
					<ruby class="product__info__wrap-rating">{{ product.rating.rate}}<span class="icon star">⭐️</span> <rt>{{ product.rating.count }} ratings</rt></ruby>
				</div>
			</div>
		</a>
	</li>

</template>

<style scoped lang="scss">
	.product {

		& > a {
			display: flex;
			flex-direction: column;
			justify-content: flex-start;
			height: 100%;
		}

		&__image {
			padding: 1em;
			background-color: hsla(204,8%,46%,.06);
			transition: 0.2s background-color ease-out;
			border-radius: 0.5em;

			figure {
				height: auto;
				aspect-ratio: 2.35/1;
				
				@media only screen and (min-width: 768px) {
					aspect-ratio: 4/3;
				}
				
				img {
					object-fit: contain;
					mix-blend-mode: multiply;
					max-height: 100%;
					max-width: 80%;
					margin: 0 auto;
				}
			}
		}

		&:hover {
			.product__image {
				background-color: hsla(204,8%,46%,.1);
			}
		}

		&__info {
			display: flex;
			flex-direction: column;
			justify-content: flex-start;
			align-items: stretch;
			gap: 0.15em;
			padding-top: 0.5em;
			height: 100%;

			&__category {
				color: rgb(88, 97, 103);
				font-size: 0.85em;
				font-weight: 300;
				margin-bottom: 0.3em;
			}

			&__title {
				font-size: 1em;
				font-weight: 500;
				min-height: 2em;
			}
			&__description {
				color: hsla(204,8%,46%,.9);
				font-weight: 300;
			}

			&__wrap {
				display: flex;
				justify-content: space-between;
				align-items: flex-end;
				gap: 1em;
				flex-wrap: wrap;
				margin-top: auto;
				justify-self: flex-end;

				&-price {

				}
				&-rating {
					text-align: right;
					vertical-align: bottom;

					.icon {
						display: inline-block;
						font-size: 0.8em;
					}
					rt {
						font-size: 0.8em;
						font-weight: 300;
					}
				}
			}
		}

	}
</style>