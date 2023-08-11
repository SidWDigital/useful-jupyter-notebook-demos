# Useful Jupyter Widget Demos

Prerequisites: Please make sure Jupyter Notebook `ipywidgets` are installed and enabled. You can look for the directions [here](https://ipywidgets.readthedocs.io/en/7.6.5/user_install.html).

## Simple Image Viewer widget

Scenario: You have a list of image file paths you wish to toggle through. This widget will set up three buttons, `Previous`, `Next`, and `Home`. We are also demoing some styling options as well.

``` python

from IPython.display import display, Image, clear_output, HTML
from ipywidgets import AppLayout
import ipywidgets as widgets
from enum import Enum

class DisplayDimensions(Enum):
    # Simple class to manage image size, general formatting, etc.
    IMAGEWIDTH = 800
    BUTTONFONTSIZE='30'
    BUTTONFONTWEIGHT='bold'
    BUTTONFONTSTYLE='italic'
    BUTTONTEXTCOLOR='white'


class ImageViewer:
    def __init__(self, image_paths):
        self.image_paths = image_paths
        self.current_index = 0
        
        
        # Create widgets
        self.title_widget = widgets.HTML() # A title
        self.title_widget.width = DisplayDimensions.IMAGEWIDTH.value
        
        # We will just initialize with the first element.
        # Alternatively, you can just create a dummy array on the fly.
        # Check the syntax for the VALUE attribute.

        file = open(image_paths[0], "rb")
        image = file.read()
        self.image_widget = widgets.Image(
            value=image,
            width=DisplayDimensions.IMAGEWIDTH.value,
            height=DisplayDimensions.IMAGEWIDTH.value
        )
        
        # Previous Button
        self.prev_button = widgets.Button(
            description='Previous',
            icon='backward',
            style = dict(
                button_color='dodgerblue',
                font_size=DisplayDimensions.BUTTONFONTSIZE.value,
                font_weight=DisplayDimensions.BUTTONFONTWEIGHT.value
            )
        )
        
        # Next Button
        self.next_button = widgets.Button(
            description='Next',
            icon='forward',
            style = dict(
                button_color='lightgreen',
                font_size=DisplayDimensions.BUTTONFONTSIZE.value,
                font_weight=DisplayDimensions.BUTTONFONTWEIGHT.value,
                font_style=DisplayDimensions.BUTTONFONTSTYLE.value,
                text_color=DisplayDimensions.BUTTONTEXTCOLOR.value
            )
        )
        
        # Home button
        self.home_button = widgets.Button(
            description='Home',
            icon='home',
            style = dict(
                button_color='mediumorchid',
                font_size=DisplayDimensions.BUTTONFONTSIZE.value,
                font_weight=DisplayDimensions.BUTTONFONTWEIGHT.value
            )
        )
        
        # Set button click handlers
        self.prev_button.on_click(self.show_prev_image)
        self.next_button.on_click(self.show_next_image)
        self.home_button.on_click(self.show_first_image)
        
        # Display widgets
        self.update_display()
        
    def update_display(self):
        image_path = self.image_paths[self.current_index]
        self.title_widget.value = fr"{image_path}"
        
        # self.image_widget.url = image_path
        clear_output(wait=True)  # Clear the previous output before displaying the new image
        
        this_file = open(image_path, "rb")
        this_image = this_file.read()
        
        self.image_widget.value = this_image
        
        
        buttons_layout = widgets.Box(
            [
                self.prev_button, 
                self.next_button, 
                self.home_button                
            ],
            # width=DisplayDimensions.IMAGEWIDTH.value
        )
        
        vb = widgets.VBox()
        vb.layout.align_items = 'center'
        vb.children = [
            self.title_widget,
            self.image_widget,
            buttons_layout
            ]
        
        display(
            vb
        )


    def show_next_image(self, b):
        self.current_index = (self.current_index + 1) % len(self.image_paths)
        self.update_display()
        
    def show_prev_image(self, b):
        self.current_index = (self.current_index - 1) % len(self.image_paths)
        self.update_display()
        
    def show_first_image(self, b):
        self.current_index = 0
        self.update_display()

# List of image file locations
image_paths = [
     'image1.jpg',
     'image2.jpg',
     'image3.jpg',
     # Add more image paths here
]

viewer = ImageViewer(image_paths)


```
