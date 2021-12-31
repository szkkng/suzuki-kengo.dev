---
title: 'Draggable Component'
date: '2021-12-30'
thumbnail: '/images/draggable-component/draggable-component.png'
---

## Introduction

![draggable-component.png](/images/draggable-component/draggable-component.png)

In this tutorial, I will explain how to create a draggable component using ComponentDragger and ComponentBoundsConstrainer classes. As shown below, the blue rectangle can be dragged inside the grey rectangle.

![finish.gif](/images/draggable-component/finish.gif)

The source code is available from the repository below:

[Draggable Component - GitHub](https://github.com/szkkng/draggable-component)

### Prerequisites

Create a project named DraggableComponent.
![new-project.png](/images/draggable-component/new-project.png)

Create a new header file named GreyRect.
![add-files.png](/images/draggable-component/add-files.png)

Add the following code to GreyRect.h

```C++:GreyRect.h
#pragma once

#include <JuceHeader.h>

class GreyRect  : public juce::Component
{
public:
    GreyRect()
    {
        addAndMakeVisible (blueRect);
    };

    void paint (juce::Graphics& g) override
    {
        auto grey = juce::Colour::fromFloatRGBA (0.28f, 0.28f, 0.28f, 1.0f);
        g.fillAll (grey.withAlpha (0.1f));

        g.setColour (grey);
        g.drawRect (getLocalBounds(), 2);
    }

    void resized () override
    {
        blueRect.setBounds (getLocalBounds().withSizeKeepingCentre (40, 40));
    }

private:
   struct  DraggableComp : public juce::Component
   {
       void paint (juce::Graphics& g) override
       {
           auto bounds = getLocalBounds().reduced (1);
           auto blue = juce::Colour::fromFloatRGBA (0.42f, 0.83f, 1.0f,  1.0f);

           g.setColour (blue.withAlpha (0.2f));
           g.fillRect (bounds);

           g.setColour (blue);
           g.drawRect (bounds, 2);
       }
   };

   DraggableComp blueRect;
};
```

In MainComponent.h, include this header file and create an object of GreyRect class.

```C++:MainComponent.h
#include "GreyRect.h"
```

```C++:MainComponent.h
class MainComponent  : public juce::Component
{
・・・
private:
    GreyRect greyRect;

    JUCE_DECLARE_NON_COPYABLE_WITH_LEAK_DETECTOR (MainComponent)
};
```

Add the following code in MainComponent.cpp and build this project.

```C++:MainComponent.cpp
MainComponent::MainComponent()
{
    setSize (600, 400);

    addAndMakeVisible (greyRect);
}

void MainComponent::paint (juce::Graphics& g)
{
    auto black = juce::Colour::fromFloatRGBA (0.08f, 0.08f, 0.08f, 1.0f);
    g.fillAll (black);
}

void MainComponent::resized()
{
    greyRect.setBounds (getLocalBounds().withSizeKeepingCentre (400, 250));
}
```

As shown below, a gray rectangle and a blue rectangle are drawn, and the blue one cannot be dragged yet.
![grey-and-blue-rect.png](/images/draggable-component/grey-and-blue-rect.png)

## ComponentDragger

![drag-component.gif](/images/draggable-component/drag-component.gif)

### Creating the object

```C++:GreyRect.h
struct DraggableComp  : public juce::Component
{
    juce::ComponentDragger dragger;
・・・
};
```

### startDraggingComponent()

```C++:GreyRect.h
・・・
struct DraggableComp  : public juce::Component
{
・・・
	void mouseDown (const juce::MouseEvent& event) override
	{
		dragger.startDraggingComponent (this, event);
	}
};
```

### dragComponent()

```C++:GreyRect.h
struct DraggableComp  : public juce::Component
{
・・・
	void mouseDrag (const juce::MouseEvent& event) override
	{
		dragger.dragComponent (this, event, nullptr);
	}
};
```

### Building

![drag-component.gif](/images/draggable-component/drag-component.gif)

## ComponentBoundsConstrainer

![disappearing-rect.gif](/images/draggable-component/disappearing-rect.gif)

### Creating the object

```C++:GreyRect.h
struct DraggableComp  : public juce::Component
{
	juce::ComponentDragger dragger;
	juce::ComponentBoundsConstrainer constrainer;
・・・
	void mouseDrag (const juce::MouseEvent& event) override
	{
		dragger.dragComponent (this, event, &constrainer);
	}
};
```

### setMinimumOnscreenAmounts()

```C++:GreyRect.h
struct DraggableComp : public juce::Component
{
	juce::ComponentDragger dragger;
	juce::ComponentBoundsConstrainer constrainer;

    DraggableComp()
    {
    	constrainer.setMinimumOnscreenAmounts (20, 20, 20, 20);
    }
・・・
};
```

![constrainer1.png](/images/draggable-component/constrainer1.png)

```C++:GreyRect.h
・・・
    DraggableComp()
    {
    	constrainer.setMinimumOnscreenAmounts (40, 40, 40, 40);
    }
・・・
```

![constrainer2.png](/images/draggable-component/constrainer2.png)

### Building

![finish.gif](/images/draggable-component/finish.gif)

## Summary

## References

- [ComponentDragger Class Reference - JUCE](https://docs.juce.com/master/classComponentDragger.html)
- [ComponentBoundsConstrainer Class Reference - JUCE](https://docs.juce.com/master/classComponentBoundsConstrainer.html)