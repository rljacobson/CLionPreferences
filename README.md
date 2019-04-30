# CLion Customizations

Repo for my settings, code styles, templates, and live templates. This git repo lives in `/Users/rjacobson/Library/Preferences/CLion<version>/jba_config`, which is a directory that syncs across my JetBrains account. See the `.gitignore` for which files I have under revision and which I ignore.

## Live Templates

Of potential interest to other people are my Live Templates. If you have your own collection, I'd love to see them.

| Shortcut | Template                                            |
| :------- | :-------------------------------------------------- |
| actor    | (class assignment constructor)                      |
| cctor    | (class copy constructor)                            |
| cin      | (creates std::cin boilerplate.)                     |
| class    | (new class)                                         |
| cout     | (creates std::cout boilerplate.)                    |
| ctor     | (class constructor)                                 |
| dtor     | (class destructor)                                  |
| fmt      | (Format using fmt library)                          |
| fn       | (Strictest function definition)                     |
| for      | (Indexed for(;;) loop)                              |
| inc      | (Include boilerplate for project headers)           |
| incl     | (Include boilerplate for system headers)            |
| iter     | (Iterate range (C++ 11))                            |
| itit     | (Iterate using begin/end member functions)          |
| mv       | (std::move an object)                               |
| nocopy   | (Prevents default copy and assignment constructors) |
| nspace   | (Surround with namespace)                           |
| try      | (try-catch clause)                                  |

## CodeStyles

I like my C++ code style, but it doesn't follow any major style guide. It features:

* Required braces on `if`, `for`, and other block structures that have a shortened version. This makes sense to me as an ergonomic concession and as a guard against misinterpretation.
* `public`/`private` do not share the class definition's indentation.
* Open braces *not* on a new line.
* Avoid `>>` in templates.
* Chopped down arguments.

It's not easy to show it in the readme, because the MarkDown style messes with word-wrap and other things. Here's a clip anyway:

```c++
/*
    Created by Robert Jacobson on 30 March 2019.
    
    Description: 
    
    Copyright (c) 2019 Robert Jacobson.        
        The MIT License

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to
    deal in the Software without restriction, including without limitation the
    rights to use, copy, modify, merge, publish, distribute, sublicense, and/or
    sell copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in
    all copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
    FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS
    IN THE SOFTWARE.
    
    */

#pragma once

#include <string>

#include "gc/allocator.hpp"
#include "gc/managed.hpp"

namespace ozaml {

    using StdString = std::basic_string<
                                            char,
                                            std::char_traits<char>,
                                            GCAllocator<char>
                                        >;

    /**
     * @brief A memory managed string on the heap.
     *
     * We can't just typedef the definition of ManagedString, because it
     * overrides toString and, while we're at it, trace.
     *
     */
    class ManagedString :
            public StdString, public Managed{

        using Managed::toString;

        std::string toString() const override{
            return (std::string &&) *this;
        }

        std::ostream &trace(std::ostream &out) const override {
            return out << this->toString();
        }
    };

    /**
     * @brief A vector of memory managed objects.
     * @tparam U
     */
    template<typename U>
    class ManagedVector: public Managed, public std::vector<U>{
        // No need to override reach, as vector copies are deep copies.
    };

    /**
     * @brief A vector of pointers to memory managed objects, i.e. managed_ptr.
     *
     * @tparam V Type of object managed_ptr points to.
     */
    template<typename V>
    class ManagedPtrVector: public Managed, public std::vector< ManagedPtr<V> >{

        /**
         * @brief As this is a vector of pointers to memory managed objects,
         *        each managed_ptr needs to be reached.
         *
         * @param gc
         */
        void reach(GarbageCollector &gc) override {
            for(auto element : this){
                element.reach(gc);
            }
        }
    }; // class ManagedVector
    
} // namespace ozaml
```

<hr>
## License

I consider this uncopyrightable, as it's just trivial configuration settings. Consider it public domain. 

The code snippet above is (C) 2019 Robert Jacobson, released under the MIT license. It is a portion of a larger MIT licensed project that will make it to GitHub soon if it hasn't already.