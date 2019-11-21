## Push Notifications

As notificações por push são  muitas vezes o canal de comunicação mais importante usado pelos aplicativos que traz interatividade e reacende o uso do aplicativo pelo usuário.

Para implementar esse recurso no react native existem algumas bibliotecas como o OneSignal, @react-native-community/push-notification-ios, react-native-firebase.


## Dependências do React Native

### OneSignal

Realizando a conta gratuita é disponibilizado Push Notifications ilimitadas em aplicações mobile e web, também fornece suporte, porém limitado nesta versão da conta. Comparadas com outras formas de realizar push notifications pode se ver que o OneSignal facilita o processo, não só com sua documentação e passo a passo mas também por incluir opções para segmentar os usuários e personalizar as notificações entre outras funcionalidades.

Após realizar a conta é necessário gerar uma chave para seu aplicativo e necessário realizar as devidas configurações para as plataformas android e iOS e depois simplemente instalar a dependencia `npm install --save react-native-onesignal` e seguir os passos conforme a [documentação](https://documentation.onesignal.com/docs/react-native-sdk-setup#section-initializing-the-sdk).

---

## Configurações para push notification

Indepentende da biblioteca é necessário realizar configurações especifícas para cada plataforma. No android é necessário configurar uma conta no  Firebase Cloud Messaging (FCM) para criar uma Firebase Sender ID. No caso do iOS é necessário uma Certificação (iOS Push Certificate) para que seja possivel o envio de push notifications.


---


## Referências 

[Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging)

[React Native integrating push notifications using FCM](https://medium.com/@anum.amin/react-native-integrating-push-notifications-using-fcm-349fff071591)

[Push Notification no IOS e Android, com React Native](https://medium.com/rocketseat/push-notification-no-ios-e-android-com-react-native-66e956c89f5f)

[OneSignal](https://documentation.onesignal.com/docs/react-native-sdk-setup#section-initializing-the-sdk)

[Push notifications on react native using firebase](https://www.instamobile.io/react-native-tutorials/push-notifications-react-native-firebase/)
