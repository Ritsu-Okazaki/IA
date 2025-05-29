###### Ritsu Okazaki IBDP Year 1 Computer Science 1 HL Internal Assessment
# Appendix

## "Minimum input screen" mentioned in RoT
```.py
from kivymd.app import MDApp
from kivymd.uix.boxlayout import MDBoxLayout
from kivymd.uix.button import MDFlatButton
from kivymd.uix.dialog import MDDialog
from kivymd.uix.label import MDLabel
from kivymd.uix.screen import MDScreen
from kivy.uix.screenmanager import ScreenManager
from kivy.lang import Builder
from kivymd.uix.textfield import MDTextField

from my_lib import DatabaseManager

class playback_dialog(MDBoxLayout):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.orientation = "vertical"
        self.spacing = "20dp"

        self.playback = MDTextField(
            hint_text="Enter playback",
            mode="rectangle",
            max_text_length= 2,
            helper_text = "0~40",
            helper_text_mode = "persistent"
        )
        self.strength = MDTextField(
            hint_text="Enter strength",
            mode="rectangle",
            max_text_length=3,
            helper_text= "0~100%",
            helper_text_mode= "persistent"
        )
        self.transition = MDTextField(
            hint_text="Enter transition",
            mode="rectangle",
            max_text_length=2,
            helper_text = "0~99s",
            helper_text_mode = "persistent"
        )
        self.add_widget(self.playback)
        self.add_widget(self.strength)
        self.add_widget(self.transition)
        self.size_hint_y = None
        self.height = "200dp"

class floor_dialog(MDBoxLayout):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.orientation = "vertical"
        self.spacing = "12dp"

        self.pan = MDBoxLayout(
            orientation="horizontal",
            size_hint_y=None,
            height="40dp",
        )
        self.left = MDFlatButton(
            text="Left",
        )
        self.middle = MDFlatButton(
            text="Middle",
        )
        self.rright = MDFlatButton(
            text="Right",
        )
        self.pan.add_widget(self.left)
        self.pan.add_widget(self.middle)
        self.pan.add_widget(self.rright)

        self.tilt = MDTextField(
            hint_text="Enter tilt",
            mode="rectangle",
            max_text_length=2,
            helper_text= "0~10",
            helper_text_mode= "persistent"
        )
        self.magnitude = MDTextField(
            hint_text="Enter magnitude",
            mode="rectangle",
            max_text_length=2,
            helper_text = "0~10",
            helper_text_mode = "persistent"
        )
        self.red = MDTextField(
            hint_text="Enter red",
            mode="rectangle",
            max_text_length=2,
            helper_text="0~10",
            helper_text_mode="persistent"
        )
        self.green = MDTextField(
            hint_text="Enter green",
            mode="rectangle",
            max_text_length=2,
            helper_text="0~10",
            helper_text_mode="persistent"
        )
        self.blue = MDTextField(
            hint_text="Enter blue",
            mode="rectangle",
            max_text_length=2,
            helper_text="0~10",
            helper_text_mode="persistent"
        )

        self.strobe = MDBoxLayout(
            orientation="horizontal",
            size_hint_y = None,
            height= "40dp",
        )
        self.fast = MDFlatButton(
            text="Fast"
        )
        self.medium = MDFlatButton(
            text="Medium"
        )
        self.slow = MDFlatButton(
            text="Slow"
        )
        self.strobe.add_widget(self.fast)
        self.strobe.add_widget(self.medium)
        self.strobe.add_widget(self.slow)

        self.transition = MDTextField(
            hint_text="Enter transition",
            mode="rectangle",
            max_text_length=2,
            helper_text="0~99s",
            height="60dp",
            helper_text_mode="persistent"
        )

        self.add_widget(self.pan)
        self.add_widget(self.tilt)
        self.add_widget(self.magnitude)
        self.add_widget(self.red)
        self.add_widget(self.green)
        self.add_widget(self.blue)
        self.add_widget(self.strobe)
        self.add_widget(self.transition)
        self.size_hint_y = None
        self.height = "500dp"

class spotlight_dialog(MDBoxLayout):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.orientation = "vertical"
        self.spacing = "12dp"

        self.onoff = MDBoxLayout(
            orientation="horizontal",
            size_hint_y=None,
            height="40dp",
        )
        self.on = MDFlatButton(
            text="On",
        )
        self.off = MDFlatButton(
            text="Off",
        )
        self.onoff.add_widget(self.on)
        self.onoff.add_widget(self.off)

        self.radiuss = MDBoxLayout(
            orientation="horizontal",
            size_hint_y=None,
            height="40dp",
        )
        self.big = MDFlatButton(
            text="Big",
        )
        self.medium = MDFlatButton(
            text="Medium",
        )
        self.small = MDFlatButton(
            text="Small",
        )
        self.radiuss.add_widget(self.big)
        self.radiuss.add_widget(self.medium)
        self.radiuss.add_widget(self.small)

        self.focus = MDBoxLayout(
            orientation="horizontal",
            size_hint_y=None,
            height="40dp",
        )
        self.sharp = MDFlatButton(
            text="Sharp",
        )
        self.medium = MDFlatButton(
            text="Medium",
        )
        self.vague = MDFlatButton(
            text="Vague",
        )
        self.focus.add_widget(self.sharp)
        self.focus.add_widget(self.medium)
        self.focus.add_widget(self.vague)

        self.behavior = MDBoxLayout(
            orientation="horizontal",
            size_hint_y=None,
            height="40dp",
        )
        self.follow = MDFlatButton(
            text="Follow",
        )
        self.left = MDFlatButton(
            text="Left",
        )
        self.middle = MDFlatButton(
            text="Middle",
        )
        self.rright = MDFlatButton(
            text="Right",
        )
        self.behavior.add_widget(self.follow)
        self.behavior.add_widget(self.left)
        self.behavior.add_widget(self.middle)
        self.behavior.add_widget(self.rright)

        self.transition = MDTextField(
            hint_text="Enter transition",
            mode="rectangle",
            max_text_length=2,
            helper_text="0~99s",
            height="60dp",
            helper_text_mode="persistent"
        )

        self.add_widget(self.onoff)
        self.add_widget(self.radiuss)
        self.add_widget(self.focus)
        self.add_widget(self.behavior)
        self.add_widget(self.transition)
        self.size_hint_y = None
        self.height = "250dp"

class audio_dialog(MDBoxLayout):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.orientation = "vertical"
        self.spacing = "30dp"

        self.filename = MDTextField(
            hint_text="Enter file name",
            mode="rectangle",
            max_text_length= 2,
            helper_text = "with file extention (.mp3)",
            helper_text_mode = "persistent"
        )

        self.startguide = MDLabel(
            text="Start time",
            size_hint_y= None,
            height= self.minimum_height,
            valign="bottom"
        )
        self.start = MDBoxLayout(
            orientation="horizontal",
            size_hint_y=None,
            spacing = "15dp",
            height="75dp",
        )
        self.minute = MDTextField(
            hint_text="Enter minute",
            mode="rectangle",
            max_text_length= 2,
            helper_text = "0~99",
            helper_text_mode = "persistent"
        )
        self.second = MDTextField(
            hint_text="Enter second",
            mode="rectangle",
            max_text_length= 2,
            helper_text = "0~59",
            helper_text_mode = "persistent"
        )
        self.start.add_widget(self.minute)
        self.start.add_widget(self.second)

        self.endguide = MDLabel(
            text="End time",
            size_hint_y= None,
            height= "2dp",
            valign = "bottom"
        )
        self.end = MDBoxLayout(
            orientation="horizontal",
            size_hint_y=None,
            spacing="15dp",
            height="75dp",
        )
        self.minute = MDTextField(
            hint_text="Enter minute",
            mode="rectangle",
            max_text_length=2,
            helper_text="0~99",
            helper_text_mode="persistent"
        )
        self.second = MDTextField(
            hint_text="Enter second",
            mode="rectangle",
            max_text_length=2,
            helper_text="0~59",
            helper_text_mode="persistent"
        )
        self.end.add_widget(self.minute)
        self.end.add_widget(self.second)

        self.transition = MDTextField(
            hint_text="Enter transition",
            mode="rectangle",
            max_text_length=2,
            helper_text = "0~99s",
            helper_text_mode = "persistent"
        )
        self.add_widget(self.filename)
        self.add_widget(self.startguide)
        self.add_widget(self.start)
        self.add_widget(self.endguide)
        self.add_widget(self.end)
        self.add_widget(self.transition)
        self.size_hint_y = None
        self.height = "350dp"

class main_screen(MDScreen):
    def on_pre_enter(self, *args):
        print("hello")
        db = DatabaseManager('identifier.sqlite')
        db.close()

    def show_dialog_box(self):
        self.text_field = MDTextField(
            hint_text="Enter something",
            mode="rectangle",
            multiline= True,
            max_height= "75dp"
        )
        self.dialog = MDDialog(
            title=f"Enter dialog",
            type="custom",
            content_cls=self.text_field,
            buttons=[
                MDFlatButton(
                    text="CONFIRM",
                    on_press=lambda x: self.dialog.dismiss(),
                    on_release=lambda x: self.dialog.dismiss(),
                ),
                MDFlatButton(
                    text="CANCEL",
                    on_release=lambda x: self.dialog.dismiss(),
                )]
        )
        self.dialog.open()

    def show_overhead_box(self):
        self.content = playback_dialog()
        self.dialog = MDDialog(
            title=f"Enter overhead",
            type="custom",
            content_cls=self.content,
            buttons=[
            MDFlatButton(
                text="CONFIRM",
                on_press=lambda x: self.dialog.dismiss(),
                on_release=lambda x: self.dialog.dismiss(),
            ),
            MDFlatButton(
                text="CANCEL",
                on_release=lambda x: self.dialog.dismiss(),
            )]
        )
        self.dialog.open()

    def show_floor_box(self):
        self.content = floor_dialog()
        self.dialog = MDDialog(
            title=f"Enter floor",
            type="custom",
            content_cls=self.content,
            buttons=[
            MDFlatButton(
                text="CONFIRM",
                on_press=lambda x: self.dialog.dismiss(),
                on_release=lambda x: self.dialog.dismiss(),
            ),
            MDFlatButton(
                text="CANCEL",
                on_release=lambda x: self.dialog.dismiss(),
            )]
        )
        self.dialog.open()

    def show_spotlight_box(self):
        self.content = spotlight_dialog()
        self.dialog = MDDialog(
            title=f"Enter spotlight",
            type="custom",
            content_cls=self.content,
            buttons=[
            MDFlatButton(
                text="CONFIRM",
                on_press=lambda x: self.dialog.dismiss(),
                on_release=lambda x: self.dialog.dismiss(),
            ),
            MDFlatButton(
                text="CANCEL",
                on_release=lambda x: self.dialog.dismiss(),
            )]
        )
        self.dialog.open()

    def show_audio_box(self):
        self.content = audio_dialog()
        self.dialog = MDDialog(
            title=f"Enter audio",
            type="custom",
            content_cls=self.content,
            buttons=[
            MDFlatButton(
                text="CONFIRM",
                on_press=lambda x: self.dialog.dismiss(),
                on_release=lambda x: self.dialog.dismiss(),
            ),
            MDFlatButton(
                text="CANCEL",
                on_release=lambda x: self.dialog.dismiss(),
            )]
        )
        self.dialog.open()

class IA(MDApp):
    def build(self):
        Builder.load_file("IA.kv")  # or your .kv file name
        sm = ScreenManager()
        sm.add_widget(main_screen(name='main_screen'))
        self.makedatabase()
        return sm

    def makedatabase(self):
        db = DatabaseManager('identifier.sqlite')
        db.run_save(
            query="""CREATE TABLE IF NOT EXISTS cues (cue_id INTEGER PRIMARY KEY UNIQUE,
             name TEXT, dialog TEXT, overhead TEXT, floor TEXT, spotlight TEXT, audio TEXT, parent_id INTEGER)""")

IA().run()
```
