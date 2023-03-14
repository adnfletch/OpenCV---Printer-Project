#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <iostream>
#include <cstdlib>
#include <sstream>
#include <fstream>
#include <iomanip>

using namespace std;
using namespace cv;

int main() {

    char blackorwhite = 'n';
    bool bw = true;
    int down_width = 0;
    int down_height = 0;

    //Ask for the size of the image for the printer. We included recommended pixel size.
    cout << endl << "Please enter the printing pixel size: Recommended: square 126x126, 16:9 224x126, 4:3 168x126" << endl
        << "Enter the image width: ";
    cin >> down_width;

    cout << endl << "Enter the image height: ";
    cin >> down_height;

    /*Load the image, should be pasted in the resource files of our c++ project.Path needs double slashes : https://www.youtube.com/watch?v=2NwIqIwsKss */
    string image_path = "C:\\Users\\aidan\\OneDrive\\Documents\\University\\MTE 100\\Final Project\\BigWeef.jpg";
    /*Create a Mat(pixel array) from the image file.We will be in RGB mode(in the array its stored as BGR).*/
    Mat img = imread(image_path, IMREAD_COLOR);

    //Check if the image could not be opened. From this source: 
    if (img.empty())
    {
        cout << "Could not read the image: " << image_path << std::endl;
        return 1;
    }

    /*Read the input image heightand width.https://stackoverflow.com/questions/32971241/how-to-get-image-width-and-height-in-opencv */
    int originalHeight = 0, originalWidth = 0;
    originalHeight = img.size().height;
    originalWidth = img.size().width;

    cout << endl << originalHeight << "x" << originalWidth << endl;


    //Set the resize width and height Resizing is from this source:

    //Create the new array for the resized small image and pixellated image.
    Mat resized_down;

    Mat pixellated;

    /*Resize the image, will create a new small image called resized_down, this is the truely smaller pixel image.*/
    resize(img, resized_down, Size(down_width, down_height), INTER_LINEAR);

    
    /*Scale up the reized_down smaller pixel image by creating pixellated image, this is only
    used to display the image on the computer. (so to the user, it is essentially "pixellating" the image). */
    resize(resized_down, pixellated, Size(originalWidth, originalHeight), INTER_LINEAR);
    
    //Create the output text File. 
    /* The image array has BGR and the red, green, and blue channels each have a value from 0-255. To store this, open cv uses a 3 part vector.
    The array can be read using the at function. The format is .at<datatype>(row,column)[RGB] and will return the pixel values. Our data type will be Vec3b, which
    stands for 3 bytes (as the values are only 0-255). For the [RGB] a 0 will be blue, a 1 will be green, and a 2 will be red.
    source: https://www.youtube.com/watch?v=S4z-C-96xfU */

    ofstream FileOut("C:\\Users\\aidan\\OneDrive\\Documents\\University\\MTE 100\\Final Project\\PrinterDownloads\\PrintData.txt");
    FileOut << resized_down.rows << " "<< resized_down.cols << endl;

    /*Will loop through the imageand output it to the text file, sidewaysand reflected.
    This is because the printer reads the file from left to right but prints 
    right to left.
    */
    
    //Create the vector and output file
    Vec3b Pixel;
    for (int column = down_width-1; column >= 0; column--)
    {
        for (int row = down_height-1; row>=0; row--)
        {
            //Get the blue(0),green(1), red(2) values.
            Pixel[0] = resized_down.at<Vec3b>(row, column)[0];
            Pixel[1] = resized_down.at<Vec3b>(row, column)[1];
            Pixel[2] = resized_down.at<Vec3b>(row, column)[2];

            //Write to the file
            //RGB all 0 is black, RGB all 255 is white
            //If it is a black pixel output a 1
            //If it is a white pixel output a 0 
            
            //Uses a range to round to black
            if (Pixel[0] <= 40 && Pixel[1] <= 40 && Pixel[2] <= 40)
            {
                FileOut << "1 ";
            }
            else
            {
                FileOut << "0 ";
            }
        }
        FileOut << endl;
    }
    FileOut.close();

    //Display the original and the pixellated image
    imshow("Display window", pixellated);
    imshow("Display2", img);
    int k = waitKey(0); // Wait for a keystroke in the window

}