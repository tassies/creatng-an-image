# creatng-an-image *use gimp to view image*
this program creates and draws an image using this pseudo object and outputs the resulting PPM string to standard output.

#include <iostream>

#include "classing.h"

#include "compression.h"

using namespace std;

int main()
{

  Flag flag = Flag();

  Colours ethopianFlagColours[4];

  //Colours are derived from wikimedia SVG of flag

  ethopianFlagColours[0].red = 7;

  ethopianFlagColours[0].green = 137;

  ethopianFlagColours[0].blue = 48;

  ethopianFlagColours[1].red = 253;

  ethopianFlagColours[1].green = 230;

  ethopianFlagColours[1].blue = 66;


  ethopianFlagColours[2].red = 218;

  ethopianFlagColours[2].green = 18;

  ethopianFlagColours[2].blue = 26;


  ethopianFlagColours[3].red = 255;

  ethopianFlagColours[3].green = 255;

  ethopianFlagColours[3].blue = 255;

  //Set background to first colour bar value

  flag.setImageColour(ethopianFlagColours[0]);

  //Calculate the height of a colour bar

  int intBarHeight = flag.getDimension().rows / 4;

  //For row pixels in colour bar 2

   for(unsigned int r = intBarHeight; r < flag.getDimension().rows / 2; r++)
    {

        coodinates recCoordinate;

        //For column pixels within the bar

        for(unsigned int c = 0; c < flag.getDimension().cols; c++)
        {

            recCoordinate.row = r;

            recCoordinate.col = c;

            //Set pixel in colour bar 2

            flag.setPixelColour(recCoordinate, ethopianFlagColours[1]);

            //Set pixel in colour bar 3

            recCoordinate.row += intBarHeight;

            flag.setPixelColour(recCoordinate, ethopianFlagColours[2]);

            //Set pixel in colour bar 4

            recCoordinate.row += intBarHeight;

            flag.setPixelColour(recCoordinate, ethopianFlagColours[3]);
         }
  
      }

  //Convert to PPM and output

  cout << flag.toString();

  return 0;
}



#include <cassert>

#include <cstdlib>

#include <iostream>

#include <string>

#include <sstream>

#include "classing.h"

using namespace std;

Flag::Flag()
{
    
    dimension.cols  = 5;//Set dimensions
    
    dimension.rows = 10;

    colours = new Colours*[dimension.rows];//Allocate outer layer of 2D dynamic array
    
    for(unsigned int r = 0; r < dimension.rows; r++)
    {
    
    colours[r] = new Colours[dimension.cols];//Allocate row array which is as large as the number of columns

       //Set initial colour
       
       Colours White;
       
       White.red = 255;
       
       White.green = 255;
       
       White.blue = 255;

          for(unsigned int c = 0; c < dimension.cols; c++)
          {

          colours[r][c] = White;//Set pixel to initial colour
          }
      }
      
}

Flag::Flag(const Flag& original)
{
    
    dimension = original.dimension;
    
    colours = original.colours;

    colours = new Colours*[dimension.rows];
    
    for(int i = 0; i < dimension.rows; i++)
    {
    
    colours[i] = original.colours[i];
    }
    
}

bool Flag::isInImage(Dimension dim, coodinates colourCoodinates)
{
    
    if(colourCoodinates.row < 0)
    {
    
    return false;
    }
    
    if(colourCoodinates.col < 0)
    {
    
    return false;
    }
    
    if(colourCoodinates.row >= dim.rows)
    {
    
    return false;
    }
    
    if(colourCoodinates.col >= dim.cols)
    {
    
    return false;
    }
    
    else
    {
    
    return true;
    }
    
}

Flag::~Flag()
{
    
    cout << "Deallocating memory" << endl;
    
    for(unsigned int r = 0; r < dimension.rows; r++)
    {
    
    delete [] colours[r];//De-allocate row array
    }
    
    delete [] colours;//De-allocate outer array
    
    colours = nullptr;//De-allocate object instance

}

Flag::Flag(const Dimension dim)
{
    
    //Allocate freestore space for pseudo-object
    
    dimension = dim;//Set dimensions
    
    colours = new Colours*[dimension.rows];//Allocate outer layer of 2D dynamic array
    
    for(unsigned int r = 0; r < dimension.rows; r++)
    {
    
    colours[r] = new Colours[dimension.cols];//Allocate row array which is as large as the number of columns
    
    //Set initial colour
    
    Colours White;
    
    White.red = 255;
    
    White.green = 255;
    
    White.blue = 255;

        for(unsigned int c = 0; c < dimension.cols; c++)
        {
          
          colours[r][c] = White;//Set pixel to initial colour
        }
    }
    
}

void Flag::setImageColour(Colours c)
{
    
    for(unsigned int i = 0; i < dimension.rows; i++)
    {
      
      for(unsigned int j = 0; j <dimension.cols; j++)
        {
           
           colours[i][j] = c; //set the colour
        }
    }
    
}

string Flag::_toString(int number)const
{
  
  stringstream ssConv;
  
  ssConv << number;
  
  return ssConv.str();

}

string Flag::toString()const
{
   
   string mgicNum = "P3\n";

//Set image dimensions in stream
    
    mgicNum += _toString(dimension.cols) + "\n";
    
    mgicNum += _toString(dimension.rows) + "\n";

//Set maximum value for colour component
    
    mgicNum += "255\n";

//Create string stream to build string
    
    stringstream ssPPM;

//Prepend header
    
    ssPPM << mgicNum;

    for(unsigned int r = 0; r < dimension.rows; r++)
    {
        
        for(unsigned int c = 0; c < dimension.cols; c++)    
    {

//Output convert colour components
           
           ssPPM <<_toString(colours[r][c].red)<<" "<<_toString(colours[r][c].green)<<" "<<_toString(colours[r][r].blue)<<" ";
        }
        
        ssPPM << endl;
    }

//Convert to string and return

return ssPPM.str();
}

void Flag::setPixelColour(coodinates colourCoodinates, Colours c)
{
   
   if(!isInImage(dimension, colourCoodinates))
{

//report error and exit
        
        cerr << "Invalid pixel location: ["
             
             << colourCoodinates.row
             
             << "," << colourCoodinates.col
             
             << "]" << endl;
        
        exit(-1);
    }

//else set the colour at that position
    
    colours[colourCoodinates.row][colourCoodinates.col] = c;
}

void Flag::setDimension(Dimension dim)
{
    
    dimension = dim;
}

void Flag::setDimension(int r, int c)
{
    
    dimension.cols = c;
    
    dimension.rows = r;
}

Dimension Flag::getDimension()const
{
    
    return dimension;
}

void Flag::setColours(Colours** c)
{
    
    colours = c;
}

Colours** Flag::getColours()const
{
    
    return colours;
}
