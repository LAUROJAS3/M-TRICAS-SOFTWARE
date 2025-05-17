# Instalar dependencias
!pip install requests beautifulsoup4 pandas

import requests
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime
import html

#URLs de los productos
urls = {
    "Amazon Xiaomi Redmi Buds 6 Lite": "https://www.amazon.com.mx/Xiaomi-Aud%C3%ADfonos-Redmi-Buds-Black/dp/B0D9YYQ7X9/ref=sr_1_1?__mk_es_MX=%C3%85M%C3%85%C5%BD%C3%95%C3%91&sr=8-1",
    "MercadoLibre Xiaomi Redmi Buds 6 Lite": "https://www.mercadolibre.com.mx/auriculares-xiaomi-redmi-buds-6-lite-cover-company-color-negro/p/MLM41279269#polycard_client=search-nordic&searchVariation=MLM41279269&wid=MLM2171470213&position=13&search_layout=stack&type=product&tracking_id=e3653358-b021-4a48-b485-3e2191914aa0&sid=search",

    "Amazon CMF Buds Pro 2":"https://www.amazon.com.mx/CMF-NOTHING-Buds-Pro-inal%C3%A1mbricos/dp/B0D544PSWC/ref=sr_1_1?__mk_es_MX=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=2F8S51M3Z3YO2&dib=eyJ2IjoiMSJ9.GdkLi9pX3Q2awkZh0jQciqNeSZTbYbnB79oUBJrKrw_3Os5kib1Oxi0kSU02lzREvGn0TObgYQZlcboKadnit2-RDfn-cc3kLlZRBmycpzHj59RmLrnJ4XPXViq2JEiY8Kl47fn7z-2r-0El5unwSfS-3FQAZXfBCuCxoYF5j8633bZoXZDmTHn0yKInRWZkj3ll-twuL9d5NntF5FtIUZtyev05lzx2k9b_GbkqslbOWjlTAwM6i2PUQ836jEdGn_zE5L1PWIcF_fD6ZmGza2kbM5Gysea9ASTFlrywY6PY2nw6wucdMv3gI26Lzi30bVM9fpKhf4S9s0Ld7gBkt6Y3CHcZz9YghgDvjhwCwavsa4ZJDtMB7PkcS4iEgjopP4bYy1R76suR8mPxaz4dmSnk01bEdAnf8MeSBO-0ht5jAz-cSxYuodfFRJgpxhkA.e2CdsI05FyV1zJnGf4X6ZLXMpZ8shfQFFYDM6TG0HXQ&dib_tag=se&keywords=CMF%2BBuds%2BPro%2B2&qid=1742621245&sprefix=cmf%2Bbuds%2Bpro%2B2%2Caps%2C272&sr=8-1&th=1",
    "MercadoLibre CMF Buds Pro 2":"https://www.mercadolibre.com.mx/cmf-by-nothing-buds-pro-2-audifono-bluetooth-53-inalambrico-color-naranja/p/MLM44220543#polycard_client=search-nordic&searchVariation=MLM44220543&wid=MLM2225549597&position=1&search_layout=stack&type=product&tracking_id=c68a17b3-2010-46a7-87e4-759e55b78971&sid=search",

    "Amazon Bmani T16":"https://www.amazon.com.mx/bmanl-Wireless-Bluetooth-Headphones-Earphones/dp/B09CPDSNQM/ref=sr_1_1?__mk_es_MX=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=2K82X5MC92HN7&dib=eyJ2IjoiMSJ9.DuuQJr5fCBiyuiOIEvjFaNsJpLL0C0gAKUAOJ9HMXqIpRwVLXT8in51F_jNQ3uzKCEWF43_B2QE3EmGI1WGUSg-gS_fru-AYoS7X2wmKzsXtPx1Al8ff-hdmfXijeSjYx2YIuWSIuaqlgO7N1O_McfOSn9ysJ5yfuj7nbXle67gclH7Koe5_PsUzX8j7cLwGcmc1UqUXVaz3oiwQJcItu41bRxGFh_4a0ps7J9NKlvIlQ9ZZ-86dxhCs0IMG6N-LRZ7tAh3qIQLKH3VGW8YeK6iP9M3gHmRJYXXY20CXuj8o2uUZ5Zlff88qvmSTl6KcKzgwIxBh4nbcNZGMTddDeDOq9zyEfkI-dRNl8nNMip-3TNu4HjI1yLEnxrMRusYGJsd7T2DXfTUFypLGZjhpfpsiihTyy9-aJ6ftwpQEPmI8xrrm_pqDP1qj_KSVsN9Y.9lPc5QUyOEWwKPFU4Us-svGvl3te6pHGKYWjEN-gtbA&dib_tag=se&keywords=Bmani%2BT16&qid=1742621330&sprefix=bmani%2Bt16%2Caps%2C281&sr=8-1&ufe=app_do%3Aamzn1.fos.45030d3a-91a9-4303-890a-776dee9077c1&th=1",
    "MercadoLibre Bmani T16":"https://www.mercadolibre.com.mx/audifonos-inalambricos-bmani-t16-auricular-dentro-de-oido-con-pantalla-digital-48-horas-movimiento-comodo-sport-negro/p/MLM26451606#polycard_client=search-nordic&searchVariation=MLM26451606&wid=MLM2918918096&position=9&search_layout=stack&type=product&tracking_id=bf39af00-4867-492c-bb64-83ba887f4a13&sid=search",

    "Amazon Nothing Ear Open":"https://www.amazon.com.mx/Nothing-auriculares-tecnolog%C3%ADa-micr%C3%B3fonos-compatible/dp/B0DGLL5CWK/ref=sr_1_1?__mk_es_MX=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=OXKLPL0LBG4E&dib=eyJ2IjoiMSJ9.659GrDM-KEG66yUOC9_MjzzzF-gXX9vQVeowze2ofbXwUNsR5sfdELo0RfZyC9ZZAjfrA2ktxh5XV_Li9R5yBig4GfkLRJBSfn_iCDIqNfsKNGcyRcYBx1WdHmO_04h0neSxPfJGEKEpqpp1EsS5YuFrVAvbjdpDr_vYL5E402MT68_qlOKXWIHe2otl1oA5KzxAX9R21Q9nDYdnlGP1BePf18N6Jf0OWxBo1-ZllYak8JGfeTzM1Ir2jSpOZpNZ2Ah5kCrEBz8hjP5a9OF_iQrkjwsXM10wH_UbKo5gfctuVUFBQxFMGGZ-OL6u0noW8nA_MeYDZhfuUcmpMyYA5BcgVYGW2dXbXpLiQH8Be8lPIjAwdpLYiywXxLh3GG-jcNEFSfYX2sxZVEVY-U5AyeP3r4l_bE4qHtwjdIF34yl2_PQuKF0wSmcgS3ss6OG7.lLAnzdDf45e_r1OkhBsu3hJFccZ96nq4xOxBx21SuOk&dib_tag=se&keywords=nothing+ear+open&qid=1742621431&sprefix=nothing+ear+open%2Caps%2C166&sr=8-1&ufe=app_do%3Aamzn1.fos.de93fa6a-174c-4df7-be7c-5bc8e9c5a71b",
    "MercadoLibre Nothing Ear Open":"https://www.mercadolibre.com.mx/nothing-ear-open-ows-audifonos-inalambricos-bluetooth-53-color-blanco/p/MLM43852038#polycard_client=search-nordic&searchVariation=MLM43852038&wid=MLM3481732388&position=1&search_layout=stack&type=product&tracking_id=8571fc29-c9a5-44de-95b8-52b80e87db2c&sid=search",

    "Amazon Redmi Buds 6 Play":"https://www.amazon.com.mx/Aud%C3%ADfonos-Redmi-Blanco-hasta-reproducci%C3%B3n/dp/B0DBHT1BT9/ref=sr_1_2?crid=15HTBN81PDZCU&dib=eyJ2IjoiMSJ9.NlJpiPLQpoA4Y0RR6Zw1V_2K8GK6I5QS-nohYsG6gx-W8owc7PCC7RaUqbZHcsMg-IeZx70BR0qjQce-9picdSU5mR4m1ev_BGYONcZzFMnj3fiCtkoyAROjcddwdpOyInCCfLebWUpG2M1F5JRvrm-aWcU3qGxXUQ7iDoLJmX-bGFNBuXU3gShz-9_xny2uYloYFW8Qg9-C6i_tZSX0ckbtMrhwDLONswEwM2Xv6y87b6Qnjh_s9RL5hg6KTu1nLk6rBp555jcxgw4iG-Ru2hIrRhnwj5tekc0psGaq--tLfgWr9uXJ3EVKzpIY0H8csAaP1NHmEDVvuFD252p_d2CEB-GCw0ahQu2IsPEhC9rtjOvLw9bCvFKhG30iX5L9jIZYaVeZrfV1PYjYtLeDj2Wc12ESJu7wPCSnnskGP6k-EIwy0JqKCifyqjEuM3u9.bqgOIRrnY5qrOX5XSfq8JyJ3L3lO72MjUhmHb9HpN50&dib_tag=se&keywords=redmi%2Bbuds%2B6%2Bplay&qid=1742621572&sprefix=Redmi%2BBuds%2B6%2BPlay%2Caps%2C190&sr=8-2&ufe=app_do%3Aamzn1.fos.de93fa6a-174c-4df7-be7c-5bc8e9c5a71b&th=1",
    "MercadoLibre Redmi Buds 6 Play":"https://www.mercadolibre.com.mx/auriculares-inalambricos-xiaomi-redmi-buds-6-play-bluetooth-54-color-negro/p/MLM39962085#polycard_client=search-nordic&searchVariation=MLM39962085&wid=MLM3573007572&position=2&search_layout=stack&type=product&tracking_id=32b2562d-d3b6-411c-a69e-76beece9e940&sid=search",

    "Amazon Sony WH-CH520":"https://www.amazon.com.mx/Sony-Aud%C3%ADfonos-inal%C3%A1mbricos-WH-CH520-duraci%C3%B3n/dp/B0CFSKN2LW/ref=sr_1_1?__mk_es_MX=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=78C4TYO3PWKA&dib=eyJ2IjoiMSJ9.W-VB_WPm1qcq-Azxd0YXfDT5Bnr93wRo9jENsPkSn7SSGvD2Ss9yyiZmMPy4jgb9q_RRQyL2FBmvqO9tuSZp95oYQTRdvG7W_odGztVBse04o9ugf3yC9a5tTJMcymjpy_Ci34VDhLuq402S8BRPesXn7Xr45WmX6-N_N0EN_rCguRaZGOwFruHAY83K9iif6_0frmhHEOnJwSWigebchQqWym2Ep78pRsvBa5YaxdeFKGnO4ka5LPrsSIEGWLgIb8C11Hs2CkSOMS1yA5HbXx-5pZdZqgIEMTHw2j8PVxavgq6dVOz8uWrF0wBFNktXtWQw53ZcqCiZj1-o6qeK3Hrl_VhKHzv5g5hRB0cjpQxcdvNGquox0mDzd6LCpwqryQCbYbeA-Bp0HDUfTihgGPF-4kjh3447c_jzCTIoxF1YM9tdnLQpEFu8Rr52JAW0.cTtSlke-aeY2ba2xpozyOum5_D1KWdzKAt1PlqoZl_E&dib_tag=se&keywords=aud%C3%ADfonos%2Bsony&qid=1742619182&sprefix=audifonos%2Bson%2Caps%2C170&sr=8-1&ufe=app_do%3Aamzn1.fos.de93fa6a-174c-4df7-be7c-5bc8e9c5a71b&th=1",
    "MercadoLibre Sony WH-CH520": "https://www.mercadolibre.com.mx/audifonos-inalambricos-sony-wh-ch520-negro/p/MLM23138487#polycard_client=search-nordic&searchVariation=MLM23138487&wid=MLM2257481316&position=6&search_layout=stack&type=product&tracking_id=e4de1637-049b-4505-8db8-df199fc4e5df&sid=search",

    "Amazon Audífonos 1Hora": "https://www.amazon.com.mx/Hora-Inal%C3%A1mbricos-Auriculares-Compatible-Computadora/dp/B0B8CT79CM/ref=sr_1_1?__mk_es_MX=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=3USB5KV44TU1C&dib=eyJ2IjoiMSJ9.Y0zjxmokl3l3tMcpEADRjC7CJbL1_wJrOW4BoA2qK6RPWMls4H0ik8z2jp9RShkCV9wb8p1hez7n1G_DVOrNH32rcLagvwXoqIwOgg53wQ3sz1EC-EjkvfvMDGGoBeILITDmu36MB7ZGnsaREV5hmMfIrw605Cp2gSIpMfbFdsQpH5sBTfGfQptf-2Nclz5vzTZ73iYLpgGLSS55c1Dg2yxLGNg5yEaIVEY3RbUEBUd-ImDQN_QcgPNo1YfCdXzPDM4psuzSVbhIhqckacUeppxK-Mit5a75VKVwGWrZxMdk-YuDBngVkpteDM_rGOKpwtjXJkIpqHoTku7l87g0r6be2K88LUa3GxqRVRy99hYpthLfftGjYTvrfxEAHx1vJ8TOrXfrF55PUjX-lWWGMClR9IS7aiI-M9cFMFhqRl4HBMxv0Q0RWoKb0JpKY5FN.M_PXyoM-_ZeAyHf62BhwkCfsel297O4CqSVw3CSxGw8&dib_tag=se&keywords=Audifonos%2B1Hora&qid=1742619697&sprefix=audifonos%2B1hora%2Caps%2C165&sr=8-1&ufe=app_do%3Aamzn1.fos.de93fa6a-174c-4df7-be7c-5bc8e9c5a71b&th=1",
    "MercadoLibre Audífonos In-Ear": "https://www.mercadolibre.com.mx/audifonos-in-ear-inalambricos-1hora-aut114-negro-bluetooth/p/MLM18627231#polycard_client=search-nordic&searchVariation=MLM18627231&wid=MLM3145628632&position=3&search_layout=stack&type=product&tracking_id=7151658d-716e-4ea7-b087-583411bcddcb&sid=search",

    "Amazon CMF Buds Blanco Claro": "https://www.amazon.com.mx/CMF-Buds-Auriculares-inal%C3%A1mbricos-reproducci%C3%B3n/dp/B0CXDDWRTQ/ref=asc_df_B0CXDDWRTQ/?gad_source=1&hvadid=709978979468&hvdev=c&hvdvcmdl=undefined&hvlocint=undefined&hvlocphy=9137861&hvnetw=g&hvpone=undefined&hvpos=undefined&hvptwo=undefined&hvqmt=undefined&hvrand=5665400651847403929&hvtargid=pla-2296354446657&language=es_MX&linkCode=df0&mcid=81306c1d6e2d33a8933e584ebd946c01&tag=gledskshopmx-20&th=1",
    "MercadoLibre KZ EDX Pro Gamer Negro": "https://www.mercadolibre.com.mx/audifonos-in-ear-kz-edx-pro-gamer-con-microfono-color-negro-con-luz-cristal/p/MLM19837562",

    "Amazon KZ EDX Pro Gamer Negro": "https://www.amazon.com.mx/Auriculares-KZ-Monitor-Deportivos-Micr%C3%B3fono/dp/B09NNXMLDY/ref=asc_df_B09NNXMLDY/?tag=gledskshopmx-20&linkCode=df0&hvadid=709953053687&hvpos=&hvnetw=g&hvrand=3196956443081376605&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-1599631507409&psc=1&mcid=3a30e307208030b3aa086c598d07e215&tag=gledskshopmx-20&linkCode=df0&hvadid=709953053687&hvpos=&hvnetw=g&hvrand=3196956443081376605&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-1599631507409&psc=1&language=es_MX&gad_source=1",
    "MercadoLibre Realme Buds T110 Azul": "https://www.mercadolibre.com.mx/realme-buds-t110-ipx5-bluetooth-53-reduccion-ruido-llamada-color-azul-luz-azul/p/MLM36971449",

    "Amazon Realme Buds T110 Azul": "https://www.amazon.com.mx/realme-Buds-T110-Bluetooth-Color/dp/B0DSM2SXMY/ref=asc_df_B0DSM2SXMY/?tag=gledskshopmx-20&linkCode=df0&hvadid=725750121480&hvpos=&hvnetw=g&hvrand=10436376314414091303&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2405601034452&psc=1&mcid=6771b6e94a973a5ba2f21fda4ef10fd4&tag=gledskshopmx-20&linkCode=df0&hvadid=725750121480&hvpos=&hvnetw=g&hvrand=10436376314414091303&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2405601034452&psc=1&language=es_MX&gad_source=1",
    "MercadoLibre Nothing Ear A Blanco": "https://www.mercadolibre.com.mx/nothing-ear-a-audifonos-inalambricos-manos-libres-tws-2024-color-blanco/p/MLM38365682",

    "Amazon Nothing Ear A Blanco": "https://www.amazon.com.mx/Nothing-Auriculares-inal%C3%A1mbricos-cancelaci%C3%B3n-micr%C3%B3fonos/dp/B0CXPRHTNB/ref=asc_df_B0CXPRHTNB/?tag=gledskshopmx-20&linkCode=df0&hvadid=709978979468&hvpos=&hvnetw=g&hvrand=3265227672867994798&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2328212589524&psc=1&mcid=5944bddbfbba3fe993afc72a606f1cf5&tag=gledskshopmx-20&linkCode=df0&hvadid=709978979468&hvpos=&hvnetw=g&hvrand=3265227672867994798&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2328212589524&psc=1&language=es_MX&gad_source=1",
    "MercadoLibre Samsung Galaxy Buds2 Pro Púrpura": "https://www.mercadolibre.com.mx/audifonos-samsung-galaxy-buds2-pro-ai-sm-r510nlvamxo-purpura-color-bora-purple/p/MLM19609253",

    "Amazon Samsung Galaxy Buds2 Pro Púrpura": "https://www.amazon.com.mx/SAMSUNG-Galaxy-Buds-Pro-SM-R510/dp/B0B8TG9R3Q/ref=asc_df_B0B8TG9R3Q/?tag=gledskshopmx-20&linkCode=df0&hvadid=709866038082&hvpos=&hvnetw=g&hvrand=11648853612516479561&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-1935674444872&psc=1&mcid=976e4d550e0f397c85bf0526ea275801&tag=gledskshopmx-20&linkCode=df0&hvadid=709866038082&hvpos=&hvnetw=g&hvrand=11648853612516479561&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-1935674444872&psc=1&language=es_MX&gad_source=1",
    "MercadoLibre Samsung Galaxy Buds 3 Pro Plata": "https://www.mercadolibre.com.mx/samsung-galaxy-buds-3-pro-r630-plata-color-plateado-luz-verde/p/MLM40496872",
    "Amazon Samsung Galaxy Buds 3 Pro Plata": "https://www.amazon.com.mx/SAMSUNG-Audifonos-inal%C3%A1mbricos-Nacional-Garant%C3%ADa/dp/B0D7F1N17B/ref=asc_df_B0D7F1N17B/?tag=gledskshopmx-20&linkCode=df0&hvadid=709953053687&hvpos=&hvnetw=g&hvrand=4390821568380678720&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2324955138136&psc=1&mcid=cc0878e3ce45333ca6dd37dff4f9f7ad&tag=gledskshopmx-20&linkCode=df0&hvadid=709953053687&hvpos=&hvnetw=g&hvrand=4390821568380678720&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2324955138136&psc=1&language=es_MX&gad_source=1",

    "MercadoLibre Apple AirPods 4 Blanco": "https://www.mercadolibre.com.mx/apple-airpods-4-color-blanco-distribuidor-autorizado/p/MLM1040520107",
    "Amazon Apple AirPods 4 Blanco": "https://www.amazon.com.mx/inal%C3%A1mbricos-Apple-Personalizado-Resistentes-configuraci%C3%B3n/dp/B0DGJ7MDGW/ref=asc_df_B0DGJ7MDGW/?tag=gledskshopmx-20&linkCode=df0&hvadid=709877922915&hvpos=&hvnetw=g&hvrand=16700626948735359613&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2363050396682&psc=1&mcid=a39c398bed7d37bcbf2764e217341d28&tag=gledskshopmx-20&linkCode=df0&hvadid=709877922915&hvpos=&hvnetw=g&hvrand=16700626948735359613&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2363050396682&psc=1&language=es_MX&gad_source=1",

    "MercadoLibre Xiaomi Redmi Buds 6 Lite": "https://www.mercadolibre.com.mx/auriculares-xiaomi-redmi-buds-6-lite-cover-company-color-negro/p/MLM41279269#polycard_client=search-nordic&searchVariation=MLM41279269&wid=MLM2171470213&position=13&search_layout=stack&type=product&tracking_id=e3653358-b021-4a48-b485-3e2191914aa0&sid=search",
    "Amazon Xiaomi Redmi Buds 6 Lite": "https://www.amazon.com.mx/Xiaomi-Aud%C3%ADfonos-Redmi-Buds-Black/dp/B0D9YYQ7X9/ref=asc_df_B0D9YYQ7X9/?tag=gledskshopmx-20&linkCode=df0&hvadid=709877922915&hvpos=&hvnetw=g&hvrand=1566766092199452708&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2346398253505&psc=1&mcid=a4968390b68f359491c701eef48f4f4d&tag=gledskshopmx-20&linkCode=df0&hvadid=709877922915&hvpos=&hvnetw=g&hvrand=1566766092199452708&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9137861&hvtargid=pla-2346398253505&psc=1&language=es_MX&gad_source=1"
}
# Se inicializa la lista para almacenar los datos
productos = []

# Función para obtener el precio y calificación de Amazon
def obtener_datos_amazon(url):
    response = requests.get(url)
    if response.status_code == 200:
        soup = BeautifulSoup(response.content, "html.parser")

        # Extraer el precio
        precio = soup.find("span", class_="a-price-whole")
        if precio:
            precio = precio.text.strip().replace(',', '')
        else:
            precio = "No disponible"

        # Extraer la calificación
        calificacion = soup.find("span", class_="a-icon-alt")
        if calificacion:
            calificacion = calificacion.text.strip()
        else:
            calificacion = "No disponible"

        return precio, calificacion
    else:
        print(f"Error al acceder a Amazon: {response.status_code}")
        return "No disponible", "No disponible"

# Función para obtener el precio y calificación de Mercado Libre
def obtener_datos_mercado_libre(url):
    response = requests.get(url)
    if response.status_code == 200:
        soup = BeautifulSoup(response.content, "html.parser")

        # Extraer el precio
        precio_element = soup.find("span", class_="andes-money-amount__fraction")
        if precio_element:
            precio = precio_element.text.strip()
        else:
            precio = "No disponible"

        # Extraer la calificación
        calificacion_element = soup.find("span", class_="ui-pdp-review__rating")
        if calificacion_element:
            calificacion = calificacion_element.text.strip()
        else:
            calificacion = "No disponible"

        return precio, calificacion
    else:
        print(f"Error al acceder a Mercado Libre: {response.status_code}")
        return "No disponible", "No disponible"
# Iterar sobre las URLs y obtener datos
for nombre_producto, url in urls.items():
    if "Amazon" in nombre_producto:
        precio, calificacion = obtener_datos_amazon(url)
    else:
        precio, calificacion = obtener_datos_mercado_libre(url)

    # Agregar los datos a la lista
    productos.append({
        "Fecha": datetime.now().strftime("%Y-%m-%d"),
        "Producto": nombre_producto,
        "Precio (MXN)": precio,
        "Calificación": calificacion,
        "URL": url
    })

# Crear DataFrame
df = pd.DataFrame(productos)

# Guardar en CSV
fecha_actual = datetime.now().strftime("%Y-%m-%d")
archivo_csv = f"Audifonos_ML_AMZ_Compacion_{fecha_actual}.csv"
df.to_csv(archivo_csv, index=False, encoding='utf-8-sig')

# Mensaje de confirmación
print(f"Datos guardados correctamente en {archivo_csv}")

# Descargar el archivo CSV (solo en Google Colab)
from google.colab import files
files.download(archivo_csv)
