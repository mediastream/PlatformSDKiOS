# Mediastream Platform SDK iOS

## Introdução

Essa biblioteca permite incluir e controlar mídias e live streams localizados na Plataform Mediastream em sua aplicação iOS.

# Como usar

## Passos iniciais

Primeiro é necessário incluir a biblioteca nas dependências de seu projeto. A forma mais fácil de fazer isso é instalá-lo usando Carthage (https://github.com/Carthage/Carthage).
Uma vez instalado o Carthage, no diretório raiz de sua aplicação, crie um arquivo Cartfile com o seguinte conteúdo:

```
github "mediastream/PlatformSDKiOS"
```

Em seguida, rode o comando:

```
carthage update
```

Não esqueça de selecionar "YES" em "Allow Arbitrary Loads", que está dentro da propriedade "App Transport Security". Além disso, permita "Required Background mode", selecionando a opção como na imagem.

![alt tag](disable_app_transport_security_and_required_background_modes.png)

Agora você já pode começar a usar a biblioteca, veja o exemplo abaixo:

```swift
import UIKit
import MediastreamPlatformSDK

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let playerConfig = MediastreamPlayerConfig()
        let mdstrm = MediastreamPlatformSDK()

        playerConfig.accountID = "577beb007f565cf33ae87fa8" // accountID is now REQUIRED
        playerConfig.environment = MediastreamPlayerConfig.Environments.QA
        playerConfig.type = MediastreamPlayerConfig.VideoTypes.LIVE
        playerConfig.id = "525431f81bc42c4539000057"

        //If you're using protected files you need add:
        //Url to get certificate needed to play protected files
        playerConfig.appCertificateUrl = "https://s3.amazonaws.com/streammanagerqa/player/drm/55117baedc01616019533551.cer"

        //Url to get license needed to play protected files
        playerConfig.drmUrl = "http://drm-fairplay-licensing.axtest.net/AcquireLicense"

        //Headers with the token of the file requested, this headers are needed to complement the main request with the drm license and certificate
        playerConfig.addDrmHeader("X-AxDRM-Message", value: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2ZXJzaW9uIjoxLCJjb21fa2V5X2lkIjoiMjdGNkNDMTEtODRGMS00MzFELTk5MDItQTZDODAwRUM4NzBCIiwibWVzc2FnZSI6eyJ0eXBlIjoiZW50aXRsZW1lbnRfbWVzc2FnZSIsImV4cGlyYXRpb25fZGF0ZSI6IjIwMTctMDUtMDFUMDc6NDU6MDIuMjM1WiIsImtleXMiOlt7ImlkIjoiYzUwODUzNjctNzA4Ni00OGFlLTgwN2MtNDMzZDhlYzBkYzRlIiwiaXYiOiIrWU91SG9wOEdoMVdHY0x1VUYvV3V3PT0ifV19fQ.u4uneeLvwWhOtvotvMBTQY2ymdNt-zr_OYiKkViXGd0")

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
| drmUrl | String | Ñao | Url para obter permissões para reproduzir arquivos com proteção drm. |
| appCertificateUrl | String | Ñao | Url para baixar o certificado necessário para descriptografar a proteção do drm |

### Métodos

| Retorno | Método | Descrição |
| --- | --- | --- |
| void | addAdCustomAttribute(key: String, value: String) | Adiciona um atributo customizado ao ad. Somente utilizado se foi passado adUrl. |
| void | addDrmHeader(key: String, value: String) | Adicione um cabeçalho, para complementar a solicitação do drmUrl. |

### Construtores

MediastreamPlayerConfig()

## Classe MediastreamPlatformSDK

MediastreamPlatformSDK cria o player para Live ou VOD da Plataforma Mediastream.

### Atributos

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| volume | Int | Não | Altera volume do vídeo e retorna volume atual. |
| currentTime | Double | Não | Posição atual do vídeo. Ao passar um novo valor ao atributo, o resultado é o mesmo que chamar o método seekTo. |

### Métodos

| Retorno | Método | Descrição |
| --- | --- | --- |
| void | setup(config: MediastreamPlayerConfig) | Configura o player. |
| void | play() | Inicia reprodução do vídeo. |
| void | pause() | Pausa reprodução do vídeo. |
| void | stop() | Pára reprodução do vídeo. Igual a pause() |
| void | seekTo(Double: position) | Altera a posição do video para o tempo especificado. |


### Eventos

MediastreamPlatoformSDK possui já integrado um gestor de eventos que pode ser usado para capturar diferentes eventos que possam ocorrer durante a reprodução.

### Uso
```swift
let mdstrm = MediastreamPlatformSDK()

mdstrm.events.listenTo(eventName: "play", action: {
    NSLog("Player is playing")
})

mdstrm.events.listenTo(eventName: "pause", action: {
    NSLog("Player is paused")
})

mdstrm.events.listenTo(eventName: "error", action: { (information: Any?) in
    if (information != nil) {
        if let info = information as? String {
            NSLog("ERROR: \(info)")
        }
    }
})
```
Available events

* play: Dispara sempre que a reprodução é iniciada
* pause: Dispara sempre que a reprodução é pausada
* error: Dispara sempre que ocorre um erro durante a reprodução

### Construtores

MediastreamPlatformSDK()

## Enum MediastreamPlayerConfig.Environments

### Opções

`PRODUCTION`, `QA`

## Enum MediastreamPlayerConfig.VideoTypes

### Opções

`LIVE`, `VOD`
