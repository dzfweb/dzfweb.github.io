---
title: How easy is it to create a plugin for NativeScript?
date: 2017-05-25 15:08:36
tags: 
- NativeScript
- plugin
---

There is a growing number of plugins available for NativeScript that are developed by the community, and the reason for this is not only the willingness and sense of contribution from developers, but also the ease of developing a plugin for NativeScript, especially after version 3.0.

<!--more-->


In this article, I’ll demonstrate how I developed NativeScript Emoji, my first UI Plugin for NativeScript. By the end of this article, I hope you’ll be interested in you the interest in developing a plugin and contributing to the community of NativeScript developers, everyone wins when this happens!.

## Por onde começar?

I will demonstrate the basics that you need to know to get started, but if you prefer, the complete documentation can be found here.

The easiest way to get a plugin for NativeScript is to use the NativeScript Plugin Seed, created by NathanWalker. This library will do all the hard work of creating the structure of a NativeScript plugin, and that’s really amazing. See how easy it is:

```
git clone https://github.com/NathanWalker/nativescript-plugin-seed.git myplugin
cd myplugin
npm run postclone
npm run setup
```

Now, with just these steps you will have created all the necessary structure to develop, test and publish your plugin, but before you start, let’s make a brief introduction about the plugin we are going to develop together in this article.

## O plugin NativeScript Emoji
The purpose of this plugin is to deliver an element to the interface to display Emojis in a friendly way. My initial idea was to have a TextView element. When setting the unicode or hexadecimal value of emoji in the corresponding property:

`<Emoji:Emoji icon="128514">` exibir :😂

The plugin would do the conversion to Emoji, and here I leave a highlight again for the NativeScript community in Slack (Haven’t joined us yet? Sign up now), which made the excellent suggestion to replace the unicode property with a friendlier and easier-to-remember value:

`<Emoji:Emoji name="joy">` exibir :😂

## Implementação para Android e iOS
Now that we already have the definition of the plugin and the structure created, get started. The first thing we need to do is define the common implementation across platforms, we know that we will have a name property that will serve both Android and iOS, this setting is made in the common file that was generated in the first step of this article .

_emoji.common.ts_

```
import { View, Property } from "ui/core/view";
export class EmojiCommon extends View {
  name: string;
}
export const nameProperty = new Property<EmojiCommon, string>(
  {
    name: "name",
    defaultValue: ""
  });
nameProperty.register(EmojiCommon);
```

As this is an ui plugin, we need to extend the View class. The attributes of the element that we will create, we define creating a new object of type Property.

The property name we define the name of the attribute and the default value of it, as the name itself suggests, we define the property defaultValue. If the value of this attribute were different from the type string, which is not our case, we would need to set the value for the property valueConverter.

The emojis encoding is distinct for each platform, so we need to have a database to store those values to query during the execution of the plugin.

_emoji.database.ts_
```
export class EmojiDatabase {
    public static list(): Array<any> {
      return [
    {
        "name": "FACE WITH TEARS OF JOY",
        "unicode": "😂",
        "hexcode": "1F602",
        "codepoint": [
            128514
        ],
        "shortname": "joy"
    }];
}
```

## Implementação para Android
Now that we have the attributes of the element that we will create, we need to implement the code responsible for generating the element corresponding to each platform. NativeScript will interpret the specific code for Android at runtime through the following file.

_emoji.android.ts_ 

```
import { nameProperty, EmojiCommon } from "./emoji.common";
import { EmojiDatabase } from "./emoji.database";
import * as utils from "tns-core-modules/utils/utils";
declare var java: any;
global.moduleMerge(EmojiCommon, exports);
export class Emoji extends EmojiCommon {
  [nameProperty.getDefault](): string {
    return '';
  }
  [nameProperty.setNative](value: string) {
    let emoji = EmojiDatabase.list().find((el) => el.shortname ##= value).codepoint[0];
    this.nativeView.setText(new java.lang.String(java.lang.Character.toChars(emoji)));
  }
  private _android: android.widget.TextView;
  get android() {
    return this.nativeView;
  }
  public createNativeView() {
    this._android = new android.widget.TextView(utils.ad.getApplicationContext());
    return this._android;
  }
}
```
The first step is to extend the common class created earlier. You must implement the createNativeView method too, which will make NativeScript understand that some element must be created in the View. In our case we will create a TextView.

And again here we have another great help from the framework, the NativeScript team has mapped all the native properties and methods of each platform and generated the definitions in TypeScript, which gives us the possibility to navigate through all these native definitions directly in the editor.

We must also define what the plugin will do when it receives the attribute name, this is defined by implementing the setNative method, which in our case will query the emoji name in the database, and based on the corresponding code, It will set the value for the previously created TextView. The getDefault method is for cases where no value is set.

## Implementação para iOS
We can copy the same implementation made for Android, and only change the parts where it is created and set the value for the native element, which in the case of iOS will be the UILabel setting as follows.

_emoji.ios.ts_ 

```
import { nameProperty, EmojiCommon } from "./emoji.common";
import { EmojiDatabase } from "./emoji.database";
global.moduleMerge(EmojiCommon, exports);
export class Emoji extends EmojiCommon {
  [nameProperty.getDefault](): number {
    return 0;
  }
  [nameProperty.setNative](value: string) {
    let emoji = EmojiDatabase.list().find((el) => el.shortname ##= value).unicode;
    let nsString = NSString.stringWithString(emoji);
    this.nativeView.text = nsString;
  }
  private _ios: UILabel;
  get ios() {
    return this.nativeView;
  }
  public createNativeView() {
    this._ios = UILabel.alloc().init();
    return this._ios;
  }
}
```

Here again it will be possible to access the definition of the native methods for iOS, in the createNativeView method a UILabel is created and in the setNative method the value for the created element is defined.

## Testando o plugin
Yes, we finished the plugin implementation and what is left is just testing. Realy fast, dont you think? To test we need to add the plugin to the demo project created in the initial steps of this article. To do this, you must execute the command:
```
npm run preparedemo
```

Add the element reference in the Page element of the main-page.xml file


```
xmlns:Emoji="nativescript-emoji"
```

And then add the element within the StackLayout


```
<Emoji:Emoji name="joy" />
```

Stating as follows:


_main-page.xml_
```
<Page xmlns="http://schemas.nativescript.org/tns.xsd" xmlns:Emoji="nativescript-emoji" navigatingTo="navigatingTo" class="page">
  
    <Page.actionBar>
        <ActionBar title="NativeScript Emoji" icon="" class="action-bar">
        </ActionBar>
    </Page.actionBar>
    <StackLayout orientation="horizontal">
        <Label text="I'm happy "> </Label>
        <Emoji:Emoji name="joy" />
        <Emoji:Emoji name="joy" />
        <Emoji:Emoji name="joy" />
    </StackLayout>
</Page>
```

Now let’s run the demo project on the emulators of each platform by executing the following commands.
```
nmm run demo.ios
npm run demo.android
```

![image](https://raw.githubusercontent.com/dzfweb/nativescript-emoji/master/emojiprintscreen.png)

## Conclusão
Hopefully you can see how fast and easy it is to develop a plugin for NativeScript, the community is very open to helping as well, as well as the Telerik team that seeks to deliver with each version tools that vision make our work as a developer much more productive and i throw the invitation to you developer who is reading this article, did you develop something customized on any project? Do you have any ideas about any tools that might be useful? Contribute with us as well.
