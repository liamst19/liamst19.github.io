---
layout: post
title: Notes on inheritance, abstract class type, and vector of pointers in C++
date: 2019-06-18
categories: C++
---

As I noted in my previous post, lately I have been trying to learn C++ by [working on an /Asteroids/-like game](https://github.com/liamst19/asteroids-study). One of my thoughts was that the shapes of the asteroids should be procedurally generated, instead of having a bitmap image with fixed number of predrawn asteroids, [which was how the example project in the book I was reading is designed](https://github.com/gameprogcpp/code/tree/master/Chapter03).

A drawing, I thought, would be made up of small pieces, such as a point/pixel, a line, a circle, etc. It would also have a method for displaying the piece to the screen. So, I presumed, there'd be an abstract class like the following:

```c++
// Base drawing element class
class DrawingElement{
public:

    DrawingElement();
    virtual ~DrawingElement();
    
    // Render to screen
    virtual void render()=0;
};
```

A `Circle`, `Line`, `Point` classes would inherit the `DrawingElement`:

```c++
// A Line
class Line: public DrawingElement{
public:

    // A line from point (x1, y1) to (x2, y2)
    Line(int x1, int y1, int x2, int y2):
        _x1(x1), _y1(y1), 
        _x2(x2), _y2(y2)
    {}
    ~Line(){}

    // Render Line to screen
    void render() override{
        // Render line from (_x1, _y1) to (_x2, _y2)
    }
    
private: 
    int _x1, _y1, _x2, _y2;
};

/* 
    Similarly for Circle, Point, etc.. 
*/
```

The `Drawing` class would contain a collection of these `DrawingElement` objects, and a method for adding an element to it. This collection would act as something like a template, moved and rotated before being rendered to screen. I'm skipping over the moving and rotating part in this post, for one because it's irrelevant to what I wanted to note, and because I have yet to figure out this part.

```c++
class Drawing{
public:

    Drawing(){};
    ~Drawing(){};
    
    // Add element to drawing
    void add_element(DrawingElement element){
        _drawing.push_back(element);
    }
    
    // Render drawing to screen
    void render(){
    
        // Iterate through drawing elements to render
        for(auto element: _drawing){
            element.render();
        }
    }

private:
    // The collection of elements that make up a single drawing
    std::vector<DrawingElement> _drawing;

}
```

The above did not compile; the compiler threw an error: `cannot declare parameter 'element' to be of abstract type 'DrawingElement'`. Apparently `std::vector` does not allow objects that are abstracted into their base class. It's true that, as the error message indicates, `DrawingElement` is not an object; it's an abstract type, a category which `Line`, `Circle`, and `Point` objects -- that have actual property values like coordinates, width, and height -- belong to.

The solution is to have a vector of *pointers*, `std::vector<DrawingElement*>`. For example, doing this from `main()` works:

```c++
int main(int argc, char* argv[]){

    Drawing drawing;

    Point      point{11, 12};
    Line       line{22, 23, 124, 125};
    Rectangle  rect{33, 34, 234, 75};
    
    // Instead of adding element objects, here we're 
    // adding pointers to the objects
    drawing.add_element_ptr(&point);
    drawing.add_element_ptr(&line);
    drawing.add_element_ptr(&rect);
    
    // Iterating through vector and calling render() for each
    drawing.render_ptrs();
}
```

But there is also a problem with this too. Suppose we have something like the following in `Drawing`, which creates some element objects and inserts pointers to a vector:

```c++
void draw_ptrs(){
    _drawing_ptrs.clear();

    Point      point{10, 15};
    Line       line{25, 25, 50, 100};
    Rectangle  rect{50, 50, 100, 75};
    
    _drawing_ptrs.push_back(&point);
    _drawing_ptrs.push_back(&line);
    _drawing_ptrs.push_back(&rect);
}
```

As soon as we leave the scope in which drawing elements are constructed, the pointers no longer work. Pointers are still there, but the element objects that the pointers point to are destroyed. It compiles, but trying to run the program will throw a runtime error: `pure virtual method called"`. Using the `new` keyword would work:

```c++
_drawing_ptrs.push_back(new Point{45, 55});
_drawing_ptrs.push_back(new Line{88, 98, 456, 987});
_drawing_ptrs.push_back(new Rectangle{879, 654, 123, 321});
```

It must be remembered that we have to clear the allocated memory later, when destructors are called for `Drawing`, for example.

```c++
~Drawing(){
    clear_drawing_ptrs();
}

// Clear pointer memories
void clear_drawing_ptrs(){
    for(auto ptr: _drawing_ptrs){
        delete ptr;
    }
    _drawing_ptrs.clear();
}
```

A better option is to use a *unique pointer*, `std::unique_ptr` in C++, one of what are called '*smart pointers*'. These are automatically deleted when all the objects containing them, such as vectors, are destroyed. And unique pointers also have the property of limiting itself to a single instance, that is, they can't be copied. So, supposing we have a vector called `_drawing_u_ptrs`,

```c++
_drawing_u_ptrs.emplace_back(element_u_ptr);
```

will not work. The pointer must be moved by calling a special function:

```c++
_drawing_u_ptrs.emplace_back(std::move(element_u_ptr));
```

Since unique pointers can't be copied, they must be referenced from a vector:

```c++
// Unique pointers must be referenced to be used
for(auto& element_ptr: _drawing_u_ptrs){
  element_ptr->render();
}
```

In this case there is no need to call `clear_drawing_ptrs` as we did with the standard pointers. It is safer because we won't run the risk of forgetting them, causing memory leaks.
