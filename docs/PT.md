# Mediastream Platform SDK iOS

## Introdução

Essa biblioteca permite incluir e controlar mídias e live streams localizados na Plataform Mediastream em sua aplicação iOS.

# Como usar

## Passos iniciais

Primeiro é necessário incluir a biblioteca nas dependências de seu projeto. A forma mais fácil de fazer isso é instalá-lo usando Carthage (https://github.com/Carthage/Carthage).
Uma vez instalado o Carthage, no diretório raiz de sua aplicação, crie um arquivo Cartfile com o seguinte conteúdo:

```
github "mediastream/MediastreamPlatformSDKiOS"
```

Em seguida, rode o comando:

```
carthage update
```

Não se esqueça de desabilitar a opção App Transport Security de sua aplicação para permitir requisições http:

![alt tag](disable_app_transport_security.png)

Agora você já pode começar a usar a biblioteca, veja o exemplo abaixo:

```swift
import UIKit
import MediastreamPlatformSDK

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let playerConfig = MediastreamPlayerConfig()
        let mdstrm = MediastreamPlatformSDK()

        playerConfig.environment = MediastreamPlayerConfig.Environments.QA
        playerConfig.type = MediastreamPlayerConfig.VideoTypes.LIVE
        playerConfig.id = "525431f81bc42c4539000057"

        self.addChildViewController(mdstrm)
        self.view.addSubview(mdstrm.view)

        mdstrm.setup(playerConfig)
        mdstrm.play()
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
}
```

## Classe MediastreamPlayerConfig

Essa classe possui as configurações do MediastreamPlatformSDK.

### Atributos

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| id | String | Sim | ID do vídeo |
| type | MediastreamPlayerConfig.VideoTypes | Sim | Tipo do vídeo. Valores possíveis: `VOD`, `LIVE` |
| environment | MediastreamPlayerConfig.Environments | Não | Ambiente em que está localizado o vídeo, `PRODUCTION` ou `QA`. Default: `PRODUCTION` |
| adUrl | String | Não | AdURL (ex: VAST). Opcional, se não especificado serão utilizadas as opções configuradas na Plataforma Mediastream. |
| accessToken | String | Não | Access token para vídeos restritos. |
| volume | Int | Não | Volume inicial do vídeo. |

### Métodos

| Retorno | Método | Descrição |
| --- | --- | --- |
| void | addAdCustomAttribute(key: String, value: String) | Adiciona um atributo customizado ao ad. Somente utilizado se foi passado adUrl. |

### Construtores

MediastreamPlayerConfig()

## Classe MediastreamPlatformSDK

MediastreamPlatformSDK cria o player para Live ou VOD da Plataforma Mediastream.

### Atributos

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| volume | Int | Não | Altera volume do vídeo e retorna volume atual. |

### Métodos

| Retorno | Método | Descrição |
| --- | --- | --- |
| void | setup(config: MediastreamPlayerConfig) | Configura o player. |
| void | addPlayerCallback(MediastreamPlayerCallback callback) | Adiciona callbacks que serão chamados quando o player entrar em modo tela cheia e sair de modo tela cheia. |
| void | play() | Inicia reprodução do vídeo. |
| void | pause() | Pausa reprodução do vídeo. |
| void | stop() | Pára reprodução do vídeo. Igual a pause() |

### Construtores

MediastreamPlatformSDK()

## Enum MediastreamPlayerConfig.Environments

### Opções

`PRODUCTION`, `QA`

## Enum MediastreamPlayerConfig.VideoTypes

### Opções

`LIVE`, `VOD`

Veja o JavaDoc em docs/javadoc/ para maiores informações.
