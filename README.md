use std::arch::x86_64::_mm_xor_si128;

static DISTANCE: f64 = 1.0; //the width we want to enlarge (global static variable)

fn main() {

    let random = vec![1.0, 9.970000267028809, 85.63346099853516, 4.634969234466553, 90.383056640625, 4.634969234466553, 90.383056640625, 26.468494415283203];
    let mut test = vec![1.0, 9.970000267028809, -10.0, -17.32, 0.0, 0.0, 10.0, -17.32]; //we just use the right angle to simplify (just for test)
    let mut arr =handle(&mut test);
    stuf(&mut arr ); //(attention here).
    IndexA(&mut arr ); //(attention here).
    arrow(&mut arr);
    ind(&mut arr);

}

fn handle(arr: &Vec<f64>) -> Vec<f64> { // handle the vector in order to cut off the former two. // it was used to add return type.
    let mut hdv: Vec<f64> = Vec::new(); // let mut hdv1:Vec<f64> = Vec::new();

    for i in 2..arr.len() {
        hdv.push(arr[i]);
    }
    println!("{:?} data cut is:", hdv);

    hdv
}

fn handle1(arr: &Vec<f64>) -> Vec<f64> { // handle the vector in order to cut off the former two. // it was used to add return type.

    let mut hdv1: Vec<f64> = Vec::new();

    for i in 0..2 {
        hdv1.push(arr[i]);
    }
    println!("{:?} data cut is:", hdv1);


    hdv1
}



fn endPointR(mut arr: &mut Vec<f64>) -> Vec<f64> { // output the two endpoint of the road on the right.

    let mod1 = mod1(&mut arr);
    let unitD2 = UnitV(&mut arr); // transmit the parameter
    let mut theta = 1.570796;
    let mut len = arr.len() ;
    let mut len1 = mod1.len();

    let mut endI :Vec<f64> = Vec::new();// x and y of the initial point.

    endI.push(- unitD2[0][0]);
    endI.push(- unitD2[0][1]);// initial endpoint

    let mut endE :Vec<f64> = Vec::new();// x and y of the end point.

    endE.push((arr[len-2]-arr[len-4])/mod1[len1-1]);
    endE.push((arr[len-1]-arr[len-3])/mod1[len1-1]); //end point

    let ReI = ClW(&mut theta ,&mut endI); // result of initial
    let ReE = ClW(&mut theta , &mut endE ); // result of the end
    let mut EndR :Vec<f64> = Vec::new();
    EndR.push(ReI[0] + arr[0]);
    EndR.push(ReI[1] + arr[1]);
    EndR.push(ReE[0] + arr[len-2]);
    EndR.push(ReE[1] + arr[len-1]);

    println!("{:?}end point of right", EndR);

    EndR
}

fn endPointL(mut arr: &mut Vec<f64>) -> Vec<f64> { // output the two endpoint of the road on the left.

    let mod1 = mod1(&mut arr);
    let unitD2 = UnitV(&mut arr); // transmit the parameter
    let mut theta = 1.570796;
    let mut len = arr.len() ;
    let mut len1 = mod1.len();

    let mut endI :Vec<f64> = Vec::new();// x and y of the initial point.

    endI.push(- unitD2[0][0]) ;
    endI.push(- unitD2[0][1]);// initial endpoint

    let mut endE :Vec<f64> = Vec::new();// x and y of the end point.

    endE.push((arr[len-2]-arr[len-4])/mod1[len1-1]);
    endE.push((arr[len-1]-arr[len-3])/mod1[len1-1]); //end point


    let ReI = CClW(&mut theta ,&mut endI); // result of initial
    let ReE = CClW(&mut theta ,&mut endE ); // result of the end

    let mut EndL :Vec<f64> = Vec::new();
    EndL.push(ReI[0] + arr[0]);
    EndL.push(ReI[1] + arr[1]);
    EndL.push(ReE[0] + arr[len-2]);
    EndL.push(ReE[1] + arr[len-1]);


    println!("{:?}end point of left", EndL);

    EndL
}


fn ClW(mut theta: &mut f64 , mut turP: &mut Vec<f64>) -> Vec<f64>{ //clockwise

    let mut turn:Vec<Vec<f64>> = vec![vec![0.0; 2]; 2] ; // direction matrix
    turn[0][0] =   theta.cos();
    turn[0][1] =   theta.sin();
    turn[1][0] = - theta.sin();
    turn[1][1] =   theta.cos();


    let mut  Re:Vec<f64> = Vec::new();// result (coordinate)
    Re.push(turn[0][0] * turP[0] + turn[0][1] * turP[1]);
    Re.push(turn[1][0] * turP[0] + turn[1][1] * turP[1]);

    Re

}

fn CClW(mut theta: &mut f64 , mut turP: &mut Vec<f64>) -> Vec<f64>{ //counterclock wise

    let mut turn:Vec<Vec<f64>> = vec![vec![0.0; 2]; 2] ; // direction matrix
    turn[0][0] =   theta.cos();
    turn[0][1] = - theta.sin();
    turn[1][0] =   theta.sin();
    turn[1][1] =   theta.cos();
    let mut  Re:Vec<f64> = Vec::new();// result (coordinate)

    Re.push(turn[0][0] * turP[0] + turn[0][1] * turP[1]);
    Re.push(turn[1][0] * turP[0] + turn[1][1] * turP[1]);

    Re


}

fn group(mut arr: &mut Vec<f64>) ->  Vec<Vec<f64>> { // so, those points become some line

    let arr1 = arr;
    let width = 2;
    let height = arr1.len() / 2;

    let mut unitD: Vec<Vec<f64>> = vec![vec![0.0; width]; height];  // unit direction calculation

    for i in (0..arr1.len() - 1).filter(|x| x % 2 == 0) { // 把原有的数据变成每两个坐标一组

        unitD[i / 2][0] = arr1[i];
        unitD[i / 2][1] = arr1[i + 1];
    }

    let width1 = 4;
    let height1 = arr1.len() / 2 - 1;
    let mut unitD1: Vec<Vec<f64>> = vec![vec![0.0; width1]; height1]; //set every two points as a group. // 每两个向量一组

    for i in 0..unitD.len() - 1 {
        unitD1[i][0] = unitD[i][0];
        unitD1[i][1] = unitD[i][1];
        unitD1[i][2] = unitD[i + 1][0];
        unitD1[i][3] = unitD[i + 1][1];
    }
    println!("{:?}",unitD1);

    unitD1

}

fn mod1(mut arr: &mut Vec<f64>) -> Vec<f64> { // calculate the mod of those lines in unitD1

    let unitD1 = group(&mut arr);

    let mut mod1: Vec < f64> = Vec::new();// thr distance between two points.

    for i in 0..unitD1.len(){

        mod1.push( ((unitD1[i][2] - unitD1[i][0] ) * (unitD1[i][2] - unitD1[i][0] ) + (unitD1[i][3]
        -unitD1[i][1]) * (unitD1[i][3]
        - unitD1[i][1])).sqrt());
    }
    println!("{:?}mod1",mod1);
    mod1
}

fn UnitV(mut arr: &mut Vec<f64>) -> Vec<Vec<f64>>{ //set this as unit vector(remember that the first paragraph are reversed)

    let unitD1 = group(&mut arr);
    let mod1 = mod1(&mut arr);
    let width2 = 4;
    let height2 = unitD1.len()-1;
    let mut unitD2:Vec<Vec<f64>> = vec![vec![0.0; width2 ]; height2]; //set every two points as a group.

    for i in 0..unitD1.len()-1{

        unitD2[i][0] = ( unitD1[i][0] - unitD1[i][2] ) /  mod1[i];
        unitD2[i][1] = ( unitD1[i][1] - unitD1[i][3] ) /  mod1[i];
        unitD2[i][2] = ( unitD1[i+1][2] - unitD1[i+1][0] ) /  mod1[i+1];
        unitD2[i][3] = ( unitD1[i+1][3] - unitD1[i+1][1] ) /  mod1[i+1]; // please remember this is different

    }
    println!("{:?}unit vector",unitD2);

    unitD2
}

fn addTwoUiV(mut arr: &mut Vec<f64>) -> Vec<Vec<f64>>{ // add two unit vector together(this are witting for judge the direction)

    let unitD2 = UnitV(&mut arr);// transmit the parameter
    let unitD1 = group(&mut arr);

    let width = 2;
    let height2 = unitD1.len()-1;

    let mut unitD4:Vec<Vec<f64>> = vec![vec![0.0; width ]; height2]; //

    for i in 0..unitD2.len(){
        if  (unitD2[i][0] + unitD2[i][2] ==0.0) && (unitD2[i][1] + unitD2[i][3] == 0.0){

            unitD4[i][0] = unitD2[i][1] ;
            unitD4[i][1] = -unitD2[i][0] ;

        }else{
            unitD4[i][0] = unitD2[i][0] + unitD2[i][2];
            unitD4[i][1] = unitD2[i][1] + unitD2[i][3];
        }
    }

    println!("{:?}mid vector",unitD4);

    unitD4
}

fn theta(mut arr: &mut Vec<f64>) -> Vec<f64> {

    let unitD2 = UnitV(&mut arr);// transmit the parameter

    let mut theta:Vec<f64> = Vec::new(); // calculate half the angle


    for i in 0..unitD2.len(){

        theta.push( ((((unitD2[i][0]* unitD2[i][2])+( unitD2[i][1]* unitD2[i][3]) )).acos()) / 2.0 );
        //half the angle
        // hthetal[i][0] = (unitD4[i][0]*( DISTANCE)/ (theta[i].tan())) ;
        // hthetal[i][1] = (unitD4[i][1]*( DISTANCE)/ (theta[i].tan()) );
    }
    println!("{:?}half the theta between two vector", theta);

    theta
}

fn broderp(mut arr: &mut Vec<f64>) -> (Vec<f64>, Vec<f64>) {

    let unitD4 = addTwoUiV(&mut arr);// transmit the parameter
    let unitD1 = group(&mut arr);
    let unitD2 = UnitV(&mut arr);
    let er = endPointR(&mut arr);
    let el = endPointL(&mut arr);
    let clo = clock(&mut arr);// direction

    let width = 2;
    let height2 = unitD1.len()-1;

    let mut broder:Vec<Vec<f64>> = vec![vec![0.0; width ]; height2]; // calculate multiply of the original coordinate.

    let theta = theta(&mut arr);// transmit the parameter

    for i in 0..unitD2.len() {
        if theta[i] == 1.5707963162581844 { // if the angle equals to 90 degree.
            broder[i][0] = (unitD4[i][0] * DISTANCE);
            broder[i][1] = (unitD4[i][1] * DISTANCE);
        } else {
            let mut d = (unitD4[i][0]*unitD4[i][0] + unitD4[i][1] * unitD4[i][1]).sqrt();
            broder[i][0] = (unitD4[i][0]/d * (DISTANCE) / (theta[i].sin()));
            broder[i][1] = (unitD4[i][1]/d * (DISTANCE) / (theta[i].sin())); //
        }
    }

    let mut ncdR:Vec<f64> = Vec::new();
    let mut ncdL:Vec<f64> = Vec::new();
    // let mut a = clo.len();
    // let mut ncdRL:Vec<Vec<f64>> = vec![vec![0.0; a + 2 ]; 2]; //set every two points as a group.
    ncdR.push(er[0]) ; //0 is right
    ncdR.push(er[1]) ;
    ncdL.push(el[0]) ;
    ncdL.push(el[1]) ;

    for i in 0..clo.len(){ // omit the last one

        ncdR.push(arr[ 2*i+2 ] + clo [ i ] * broder[i][0] )    ; //0 is right
        ncdR.push((arr[ 2*i+3 ] + clo [ i ] * broder[i][1] )) ;
        ncdL.push(arr[ 2*i+2 ] + (-1.0)* clo [ i ] * broder[i][0] );
        ncdL.push(arr[ 2*i+3 ] + (-1.0)* clo [ i ] * broder[i][1] );
        //
        // } if it is zhe line , then use this method arr2 is direction and change distance into 1 rather than 0.5
    }
    ncdR.push(er[2])     ; //0 is right
    ncdR.push(er[3])     ;
    ncdL.push(el[2])     ;
    ncdL.push(el[3])     ;

    println!("{:?}broder point", broder);
    println!("{:?}ncdR", ncdR);
    println!("{:?}ncdL", ncdL);


    (ncdR , ncdL) //two broder road.

}

fn caligraph(mut arr: &mut Vec<f64>) -> Vec<Vec<f64>> { // this is a ancillary points

    let (ncdR , ncdL) = broderp(&mut arr);
    let unitD4 = addTwoUiV(&mut arr);
    let clo = clock(&mut arr);// transmit the parameter
    let er = endPointR(&mut arr);
    let el = endPointL(&mut arr);
    let theta = theta(&mut arr);


    let mut a = clo.len();
    let mut Or: Vec<f64> = Vec::new();


    // let mut cal:Vec<Vec<f64>> = vec![vec![0.0; 2]; 2] ; //
    //
    // for i in 0..unitD2.len() {
    //     if theta[i] == 1.5707963162581844 { // if the angle equals to 90 degree.
    //         cal[i][0] = (unitD4[i][0] * 0.5 *DISTANCE);
    //         cal[i][1] = (unitD4[i][1] * 0.5 * DISTANCE);
    //     } else {
    //         cal[i][0] = (unitD4[i][0] * (0.5 * DISTANCE) / (theta[i].tan()));
    //         cal[i][1] = (unitD4[i][1] * (0.5 * DISTANCE) / (theta[i].tan())); // 之后或许会用得到 暂时先抛弃掉
    //     }
    // }
    let mut sort:Vec<Vec<f64>> = vec![vec![0.0; 6]; a] ; //
    for i in 0..clo.len(){
        sort[0][0] = ncdR[2 * i] ;
        sort[0][1] = ncdR[2 * i + 1];
        sort[0][2] = ncdR[2 * (i + 1)] ;
        sort[0][3] = ncdR[2 * (i + 1) +1]  ;
        sort[0][4] = ncdR[2 * (i + 2)] ;
        sort[0][5] = ncdR[2 * (i + 2) + 1];
    }
    println!("{:?}sort of right circle", sort);
    sort

}
fn caligraphL(mut arr: &mut Vec<f64>) -> Vec<Vec<f64>> { // this is a ancillary point

    let (ncdR , ncdL) = broderp(&mut arr);
    let unitD4 = addTwoUiV(&mut arr);
    let clo = clock(&mut arr);// transmit the parameter
    let er = endPointR(&mut arr);
    let el = endPointL(&mut arr);
    let theta = theta(&mut arr);


    let mut a = clo.len();
    let mut Or: Vec<f64> = Vec::new();

    let mut sort:Vec<Vec<f64>> = vec![vec![0.0; 6]; a] ; //
    for i in 0..clo.len(){
        sort[0][0] = ncdL[2 * i] ;
        sort[0][1] = ncdL[2 * i + 1];
        sort[0][2] = ncdL[2 * (i + 1)] ;
        sort[0][3] = ncdL[2 * (i + 1) +1]  ;
        sort[0][4] = ncdL[2 * (i + 2)] ;
        sort[0][5] = ncdL[2 * (i + 2) + 1];
    }

    println!("{:?}sort of left circle", sort[0]);
    sort

}





fn mergeScatR(mut arr: &mut Vec<f64>) -> Vec<f64> { // smoothing scatter point of right // diaoyongde yeshi
    let mut sort = caligraph(&mut arr);
    let theta = theta(&mut arr);
    let clo = clock(&mut arr);
    // let (mut temp,mut temp1) = rectangR(&mut sort[i]);

    // let mut temp:Vec<f64> = Vec::new();
    // let mut temp1:Vec<f64> = Vec::new(); // large
    let mut roundR = Vec::new(); //

    for i in 0..sort.len(){

        if theta[i] ==1.5707963162581844 { // maybe later we could change it into a specific range.
            if clo[i] > 0.0{
                let ( temp1, temp) = rectangR(&mut sort[i]);
                roundR.extend(temp.iter().copied())
            }else {
                let (temp, temp1) = rectangR(&mut sort[i]);
                roundR.extend(temp.iter().copied())
            } // temp.extend_from_slice(&t); } // temp.push(sort[i][4]); // temp.push(sort[i][5]) //


        // }else if theta[i] < 1.5707963162581844{  // temp = acutAR(&mut sort[i]);
        //     if clo[i] > 0.0{
        //         (temp1,temp) = acutAR(&mut sort[i]);
        //
        //     }else {
        //         (temp,temp1) = acutAR(&mut sort[i]); // temp.extend_from_slice(&t);
        //
        //     } // temp.push(sort[i][4]); // temp.push(sort[i][5]) //

        }else { // temp = bluntAR(&mut sort[i]);
            if clo[i] > 0.0{
                let ( temp1, temp) = acutAR(&mut sort[i]);
                roundR.extend(temp.iter().copied())

            }else {
                let ( temp, temp1) = acutAR(&mut sort[i]); // temp.extend_from_slice(&t);
                roundR.extend(temp.iter().copied())

            } // temp.push(sort[i][4]); // temp.push(sort[i][5]) //
        }
        // for i in 0..temp.len(){
        //     roundR.push(temp[i]);
        //     }
    }

    println!("{:?}roundR is", roundR);
    roundR

}
fn mergeScatL(mut arr: &mut Vec<f64>) -> Vec<f64>{ // smoothing scatter point of left
    let mut sort = caligraphL(&mut arr);
    let theta = theta(&mut arr);
    let clo = clock(&mut arr); // let (mut temp,mut temp1) = rectangR(&mut sort[i]); // let mut temp:Vec<f64> = Vec::new(); // let mut temp1:Vec<f64> = Vec::new(); // large
    let mut roundL = Vec::new(); //

    for i in 0..sort.len(){

        if theta[i] ==1.5707963162581844 { // maybe later we could change it into a specific range.
            if clo[i] > 0.0{
                let ( temp1, temp) = rectangR(&mut sort[i]);
                roundL.extend(temp1.iter().copied())

            }else {
                let ( temp, temp1) = rectangR(&mut sort[i]);
                roundL.extend(temp1.iter().copied()) // temp.extend_from_slice(&t);

            } // temp.push(sort[i][4]); // temp.push(sort[i][5]) //


        }else { // temp = bluntAR(&mut sort[i]);
            if clo[i] > 0.0{
                let (temp1, temp)  = acutAR(&mut sort[i]);
                roundL.extend(temp1.iter().copied());

            }else {
                let ( temp, temp1)  = acutAR(&mut sort[i]);
                roundL.extend(temp1.iter().copied());

            } // temp.push(sort[i][4]); // temp.push(sort[i][5]) //
        }
    }
    println!("{:?}roundL is", roundL);
    roundL

}

fn rectangR(mut arr: &mut Vec<f64>) -> (Vec<f64>, Vec<f64>) { // only transmit three points. arr including only 6 number.!!!!!!!!!!!!!!

    let mut ang:Vec<Vec<f64>> = vec![vec![0.0; 2]; 2] ; // direction matrix
    let mut unitD2 = UnitV(&mut arr);
    let mut mod1 = mod1(&mut arr);
    let clo = clock(&mut arr);// transmit the parameter

    let n = 6.0;
    let mut theta1:f64 = 0.261667 as f64;

    let mut E: Vec<f64> = Vec::new();
    let mut temp:Vec<f64> = Vec::new();// let mut x = arr[0] + (- unitD2[0][0]* (mod1[0]-2.5 * DISTANCE)) ; // let mut y = arr[1] + (- unitD2[0][1]* (mod1[0]-2.5 * DISTANCE)) ;

    let mut m:f64 = - unitD2[0][0]; //起始向量的单位向量
    let mut n:f64 = - unitD2[0][1];
    temp.push(m);
    temp.push(n);

    // let mut E1: Vec<f64> = Vec::new(); // E1 直接就是点的坐标， 我们在E1的基础上进行点的连续性 曲率更改
    let mut end:Vec<f64> = Vec::new();
    if clo[0] > 0.0{
        let mut l = ClW(&mut theta1 , &mut arr);

    }else {
        let mut K = CClW(&mut theta1 , &mut arr); // try not call the package, because it consume a lot of storage. //
    }


    for i in 0..5{ // easy iteration

        let end =  ClW(&mut theta1 , &mut temp);//(ang[0][0] * m + ang[0][1] * n)  ; //(ang[1][0] * m + ang[1][1] * n)   ;

        m = end[0] ;
        n = end[1] ;
        temp[0] = (m);
        temp[1] = (n);

        E.push(m);
        E.push(n);

    }
    let mut x = arr[0] + (- unitD2[0][0]* (mod1[0]-2.5 * DISTANCE)) ;
    let mut y = arr[1] + (- unitD2[0][1]* (mod1[0]-2.5 * DISTANCE)) ;
    let mut x1 = arr[0] + (- unitD2[0][0]* (mod1[0]-2.5 * DISTANCE * (1.0 - theta1.tan()) )) ;
    let mut y1 = arr[1] +(- unitD2[0][1]* (mod1[0]-2.5 * DISTANCE * (1.0 - theta1.tan()) )) ;

    let mut E1: Vec<f64> = Vec::new(); // E1 直接就是点的坐标， 我们在E1的基础上进行点的连续性 曲率更改
    E1.push(x);
    E1.push(y);
    E1.push(x1 ); //zhanf=
    E1.push(y1);
    // let mut l =2.5;
    // let mut k =2.5;

    for i in (0..E.len()-1).filter(|x| x % 2 == 0){

        x1 += ((2.5 * DISTANCE)/theta1.cos())*(theta1/2.0).sin()*2.0 *E[i] ;
        y1 += ((2.5 * DISTANCE)/theta1.cos())*(theta1/2.0).sin()*2.0 *E[i+1] ; //doesnt matter if it is same.

        E1.push(x1 );
        E1.push(y1);

    }

    let mut E2: Vec<f64> = Vec::new(); // E1 直接就是点的坐标， 我们在E1的基础上进行点的连续性 曲率更改

    let mut u = arr[0] + (- unitD2[0][0]* (mod1[0]-0.5 * DISTANCE)) ;
    let mut v = arr[1] + (- unitD2[0][1]* (mod1[0]-0.5 * DISTANCE)) ;
    let mut u1 = arr[0] +     (- unitD2[0][0]* (mod1[0]-0.5 * DISTANCE * (1.0 - theta1.tan()) )) ;
    let mut v1 = arr[1] +     (- unitD2[0][1]* (mod1[0]-0.5 * DISTANCE * (1.0 - theta1.tan()) )) ;
    E2.push(u);
    E2.push(v);
    E2.push(u1 );
    E2.push(v1);
    // let mut l =2.5;
    // let mut k =2.5;

    for i in (0..E.len()-1).filter(|x| x % 2 == 0){

        u1 += ((0.5 * DISTANCE)/theta1.cos())*(theta1/2.0).sin()*2.0 *E[i] ;
        v1 += ((0.5 * DISTANCE)/theta1.cos())*(theta1/2.0).sin()*2.0 *E[i+1] ; //doesnt matter if it is same.

        E2.push(u1);
        E2.push(v1);

    }


    // println!("{:?}end", end);
    println!("{:?}E", E);
    println!("{:?}E1", E1);
    println!("{:?} E2 is" , E2);
    (E1 , E2)    //

}

fn acutAR(mut arr: &mut Vec<f64> ) -> (Vec<f64>, Vec<f64>) {

    let mut ang:Vec<Vec<f64>> = vec![vec![0.0; 2]; 2] ; // direction matrix
    let theta =theta(&mut arr) ; //
    let mut unitD2 = UnitV(&mut arr);
    let mut mod1 = mod1(&mut arr);
    let clo = clock(&mut arr);// transmit the parameter

    let n = 6.0;
    let mut theta1:f64 = ((3.14 - 2.0 * theta[0]) / 6.0) as f64;

    let mut E: Vec<f64> = Vec::new();
    let mut temp:Vec<f64> = Vec::new();// let mut x = arr[0] + (- unitD2[0][0]* (mod1[0]-2.5 * DISTANCE)) ; // let mut y = arr[1] + (- unitD2[0][1]* (mod1[0]-2.5 * DISTANCE)) ;

    let mut m:f64 = - unitD2[0][0]; //起始向量的单位向量
    let mut n:f64 = - unitD2[0][1];
    temp.push(m);
    temp.push(n);

    // let mut E1: Vec<f64> = Vec::new(); // E1 直接就是点的坐标， 我们在E1的基础上进行点的连续性 曲率更改
    let mut end:Vec<f64> = Vec::new();
    if clo[0] > 0.0{
        let mut l = ClW(&mut theta1 , &mut arr);

    }else {
        let mut K = CClW(&mut theta1 , &mut arr); // try not call the package, because it consume a lot of storage. //
    }


    for i in 0..5{ // iterator
        let end =  ClW(&mut theta1 , &mut temp);//(ang[0][0] * m + ang[0][1] * n)  ; //(ang[1][0] * m + ang[1][1] * n)   ;

        m = end[0] ;
        n = end[1] ;
        temp[0] = (m);
        temp[1] = (n);

        E.push(m);
        E.push(n);

    }
    let mut x = arr[0] + (- unitD2[0][0]* (mod1[0]-(2.5 * DISTANCE)/theta1.tan())) ;
    let mut y = arr[1] + (- unitD2[0][1]* (mod1[0]-(2.5 * DISTANCE)/theta1.tan())) ;
    let mut x1= arr[0] +     (- unitD2[0][0]* (mod1[0]-(2.5 * DISTANCE)/theta1.tan()* (1.0 - theta1.tan()) )) ;
    let mut y1= arr[1] +     (- unitD2[0][1]* (mod1[0]-(2.5 * DISTANCE)/theta1.tan() * (1.0 - theta1.tan()) )) ;

    let mut E1: Vec<f64> = Vec::new(); // E1 直接就是点的坐标， 我们在E1的基础上进行点的连续性 曲率更改
    E1.push(x);
    E1.push(y);
    E1.push(x1 ); //zhanf=
    E1.push(y1);
    // let mut l =2.5;
    // let mut k =2.5;

    for i in (0..E.len()-1).filter(|x| x % 2 == 0){

        x1 += (((2.5 * DISTANCE))/theta1.cos())*(theta1/2.0).sin()*2.0 *E[i] ;
        y1 += (((2.5 * DISTANCE))/theta1.cos())*(theta1/2.0).sin()*2.0 *E[i+1] ; //doesnt matter if it is same.

        E1.push(x1 );
        E1.push(y1);

    }

    let mut E2: Vec<f64> = Vec::new(); // E1 直接就是点的坐标， 我们在E1的基础上进行点的连续性 曲率更改

    let mut u = arr[0] + (- unitD2[0][0]* (mod1[0]-(0.5 * DISTANCE)/theta1.tan())) ;
    let mut v = arr[1] + (- unitD2[0][1]* (mod1[0]-(0.5 * DISTANCE)/theta1.tan())) ;
    let mut u1 = arr[0] +     (- unitD2[0][0]* (mod1[0]-(0.5 * DISTANCE)/theta1.tan() * (1.0 - theta1.tan()) )) ;
    let mut v1 = arr[1] +     (- unitD2[0][1]* (mod1[0]-(0.5 * DISTANCE)/theta1.tan() * (1.0 - theta1.tan()) )) ;
    E2.push(u);
    E2.push(v);
    E2.push(u1 );
    E2.push(v1);
    // let mut l =2.5;
    // let mut k =2.5;

    for i in (0..E.len()-1).filter(|x| x % 2 == 0){

        u1 += (((0.5 * DISTANCE))/theta1.cos())*(theta1/2.0).sin()*2.0 *E[i] ;
        v1 += (((0.5 * DISTANCE))/theta1.cos())*(theta1/2.0).sin()*2.0 *E[i+1] ; //doesnt matter if it is same.

        E2.push(u1);
        E2.push(v1);

    }



    println!("{:?}end", end);
    println!("{:?}E", E);
    println!("{:?}E1", E1);
    print!("{} x is" , x);
    (E1 , E2)    //

}
// fn bluntAR(mut arr: &mut Vec<f64>)-> Vec<f64>{
//
//
// }
fn clock(mut arr: &mut Vec<f64>) -> Vec<f64>{ // judge the direction change of the road.  // first pass the value in:

    let width2 = 4;
    let height2 = arr.len()/2-2;
    let mut unitD6:Vec<Vec<f64>> = vec![vec![0.0; width2 ]; height2]; //set every two points as a group.
    // that is the calculation of the vector
    for i in (0..arr.len()-4).filter(|x| x % 2 ==0){

        unitD6[i/2][0] = ( arr[i+2] - arr[i] ) ;
        unitD6[i/2][1] = ( arr[i+3] - arr[i+1]  ) ;
        unitD6[i/2][2] = ( arr[i+4] - arr[i+2] ) ;
        unitD6[i/2][3] = ( arr[i+5] - arr[i+3]  ) ;
        // process the cross multiply

    }
    let mut clo:Vec<f64> = Vec::new();
    for i in 0..unitD6.len(){
        if unitD6[i][0]*unitD6[i][3] - unitD6[i][1]*unitD6[i][2] <= 0.0{ //clockwise
            clo.push(1.0 );

        }else{
            clo.push(-1.0 );
        }

    }

    println!("{:?}d6",unitD6);
    println!("{:?}clock",clo);

    clo

}

// Remember  to wright a index function:！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！很重要
fn IndexA(mut arr: &mut Vec<f64>) ->Vec<i32> { // that is the method of derive the index of the arrow
    let rou =mergeScatR(&mut arr);
    let roul =mergeScatL(&mut arr);

    let mut inda:Vec<i32> = Vec::new();
    let mut len= rou.len() + 4;
    let mut o = roul.len();

    let mut count = (len / 2) -1 ;

    for i in 0..=count{
        i as i32;
        inda.push(i as i32);

    }
    let mut inda1 = Vec::new();
    for i in 0..inda.len() {
        inda1.push(inda[i] * 4 +3);
        inda1.push(inda[i] * 4);
        inda1.push(inda[i] * 4 +2);
        inda1.push(inda[i] * 4 +2);
        inda1.push(inda[i] * 4 );
        inda1.push(inda[i]* 4 + 1);
    }

    let mut inda2 = Vec::new();
    for i in 0..inda.len() {
        inda2.push(inda[i] * 4 );
        inda2.push(inda[i] * 4 +1 );
        inda2.push(inda[i] * 4 +3);
        inda2.push(inda[i] * 4 +2);

    }// modify it such that it become the ordered vector with length of the count
    print!("{:?}",inda);
    print!("{:?}inda1", inda1);
    print!("{:?}inda2", inda2);
    println!("{:} o is" , o);

    inda1

}




fn stuf(mut arr: &mut Vec<f64> ) -> Vec<f64> { // calling two vector: mid point and end point
    // 回头在这里加一个判断：设置一个初始值
    let EL = endPointL(&mut arr); //transmit the parameter.
    let ER = endPointR(&mut arr);
    let clo = clock(&mut arr);// direction
    let amend = handle1(&mut arr);
    let roundR = mergeScatR(&mut arr);
    let roundL = mergeScatL(&mut arr);


    let mut Rline: Vec<f64> = Vec::new(); //right road

    Rline.push(ER[0]);
    Rline.push(ER[1]);
    for i in 0..roundR.len() {
        Rline.push(roundR[i]);
    }
    Rline.push(ER[2]);
    Rline.push(ER[3]);

    let mut Lline: Vec<f64> = Vec::new(); //left road

    Lline.push(EL[0]);
    Lline.push(EL[1]);
    for i in 0..roundL.len() {
        Lline.push(roundL[i]);
    }
    Lline.push(EL[2]);
    Lline.push(EL[3]);


    let mut Bond: Vec<f64> = Vec::new(); // sketch the boundary of the road

    for i in (0..Lline.len() - 3).filter(|x| x % 2 == 0) {
        Bond.push(Rline[i]);
        Bond.push(Rline[i + 1]);
        Bond.push(Rline[i + 2]);
        Bond.push(Rline[i + 3]);

        Bond.push(Lline[i + 2]);
        Bond.push(Lline[i + 3]);
        Bond.push(Lline[i]);
        Bond.push(Lline[i + 1]);
    }

    let mut arf: Vec<f64> = Vec::new();
    arf.push(0.2784); // RBG color index, which is fixed
    arf.push(0.2784);
    arf.push(0.2784);
    for i in (0..Bond.len() - 1).filter(|x| x % 2 == 0) {
        arf.push(Bond[i]);
        arf.push(Bond[i + 1]); // add the new coordinate into the matrix
        arf.push(0.009999999776482582);// all of the rest are fixed number
        arf.push(0.0);
        arf.push(0.0);
        arf.push(1.0);
        arf.push(0.3411764705882353);
        arf.push(0.5058823529411764);
        arf.push(1.0);
        arf.push(9.970000267028809);
    }

    println!("{:?}line is......................................................:", Lline);
    println!("{:?}RLINE :........................................................", Rline);

    println!("{:?}arf :", arf);


    println!("{:?} arf is:", arf); // just temporarily forget the arrow the current thought is to set the ratio

    Bond
}



fn arrow(mut arr: &mut Vec<f64>) -> Vec<f64> {

    let EL = endPointL(&mut arr); //transmit the parameter.
    let ER = endPointR(&mut arr);
    let clo = clock(&mut arr);// direction
    let amend = handle1(&mut arr);
    let roundR = mergeScatR(&mut arr);
    let roundL = mergeScatL(&mut arr);
    let unitD3 = UnitVS(&mut arr);
    let mut roundL = mergeScatL(&mut arr);
    let mut roundR = mergeScatR(&mut arr);
    let unit = UnitVS(&mut roundL);


    let mut Rline: Vec<f64> = Vec::new(); //right road

    Rline.push(ER[0]);
    Rline.push(ER[1]);
    for i in 0..roundR.len() {
        Rline.push(roundR[i]);
    }
    Rline.push(ER[2]);
    Rline.push(ER[3]);

    let mut Lline: Vec<f64> = Vec::new(); //left road

    Lline.push(EL[0]);
    Lline.push(EL[1]);
    for i in 0..roundL.len() {
        Lline.push(roundL[i]);
    }
    Lline.push(EL[2]);
    Lline.push(EL[3]);


    let mut interval = 1.5;
    let mut distance = 1.0 ;
    let mut arrowI: Vec<f64> = Vec::new(); //to calculate how many arrow could be stuffed into the road.
    let n = 3.0 ;//
    for i in (0..Lline.len()).filter(|x| x % 14 == 0){
        let mut len = ((Lline[i + 2] - Lline[i]) * (Lline[i + 2] - Lline[i]) + (Lline[i + 3] - Lline[i + 1]) * (Lline[i + 3] - Lline[i + 1]) ).sqrt(); //write a two layer loop
        let a = (len / (interval + distance)).round() as i32 ;
            for j in 0..a{
                arrowI.push(Rline[i] + (unitD3[i / 7] ) * ((j + 1) as f64 * interval + j as f64 * distance) );
                arrowI.push(Rline[i + 1] + (unitD3[i / 7 + 1]) * ((j + 1)  as f64 * interval + j  as f64 * distance) );
                arrowI.push(Rline[i] + (unitD3[i / 7]) * ((j + 1) as f64 * interval + (j + 1) as f64 * distance) );
                arrowI.push(Rline[i + 1] + (unitD3[i / 7 + 1] )  * ((j + 1) as f64 * interval + (j + 1) as f64 * distance) ); // two for the right

                arrowI.push(Lline[i] + (unitD3[i / 7] ) * ((j + 1) as f64 * interval + (j + 1) as f64 * distance) );
                arrowI.push(Lline[i + 1] + (unitD3[i / 7 + 1] ) * ((j + 1) as f64 * interval + (j + 1) as f64 * distance) );//two for the left.
                arrowI.push(Lline[i] + (unitD3[i / 7]) * ((j + 1)as f64 * interval + j as f64 * distance) );
                arrowI.push(Lline[i + 1] + (unitD3[i / 7 + 1]) * ((j + 1)as f64 * interval + j as f64 * distance) ); // just delete the rest two point which including 4 numbers.


            }


    }

    for j in (0..roundL.len()).filter(|x| x % 14 == 0){ // later you could add a judge

        arrowI.push(roundR[j + 2] );
        arrowI.push(roundR[j + 3]  );//two for the left.
        arrowI.push(roundR[j + 2] + (unit[j / 7 + 2]) * distance );
        arrowI.push(roundR[j + 3] + (unit[j / 7 + 3]) * distance );

        arrowI.push(roundL[j + 2] + (unit[j / 7 + 2]) *  distance );
        arrowI.push(roundL[j + 3] + (unit[j / 7 + 3]) *  distance ); // two for the right
        arrowI.push(roundL[j + 2] ) ;
        arrowI.push(roundL[j + 3] ) ;


        arrowI.push(roundR[j + 10] );
        arrowI.push(roundR[j + 11]  );//two for the left.
        arrowI.push(roundR[j + 10] + (unit[j / 7 + 6]) * distance );
        arrowI.push(roundR[j + 11] + (unit[j / 7 + 7]) * distance );

        arrowI.push(roundL[j + 8] + (unit[j / 7 + 6]) *  distance );
        arrowI.push(roundL[j + 9] + (unit[j / 7 + 7]) *  distance ); // two for the right
        arrowI.push(roundL[j + 8] ) ;
        arrowI.push(roundL[j + 9] ) ;




    }


    let mut verA =   Vec::new();
    for i in (0..arrowI.len()).filter(|x| x % 8 == 0){ //each 4 points has 4 arrow points.
        verA.push(arrowI[i]);
        verA.push(arrowI[i + 1] ); //because it is double distance
        verA.push(0.009999999776482582);
        verA.push(0.0);
        verA.push(1.0);
        verA.push(9.970000267028809);


        verA.push(arrowI[i + 2]);
        verA.push(arrowI[i + 3] );
        verA.push(0.009999999776482582);
        verA.push(0.0);
        verA.push(0.0);
        verA.push(9.970000267028809);

        verA.push(arrowI[i + 4]);
        verA.push(arrowI[i + 5]);
        verA.push(0.009999999776482582);
        verA.push(1.0);
        verA.push(0.0);
        verA.push(9.970000267028809);

        verA.push(arrowI[i + 6]);
        verA.push(arrowI[i + 7] );
        verA.push(0.009999999776482582);
        verA.push(1.0);
        verA.push(1.0);
        verA.push(9.970000267028809);
    }


    println!("{:?} arrow is :", arrowI); // just temporarily forget the arrow the current thought is to set the ratio
    println!("{:?} vertex of arrow is  :", verA);
    println!("{:?} unit !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!is  :", unit);

    arrowI
}


fn UnitVS(mut arr: &mut Vec<f64>) -> Vec<f64>{ //set this as unit vector(remember that the first paragraph are reversed)

    let unitD1 = group(&mut arr);
    let mod1 = mod1(&mut arr);
    let width2 = 4;
    let height2 = unitD1.len()-1;
    let mut unitD2:Vec<Vec<f64>> = vec![vec![0.0; width2 ]; height2]; //set every two points as a group.
    let b = unitD2.len();

    for i in 0..unitD1.len()-1{

        unitD2[i][0] = ( unitD1[i][2] - unitD1[i][0] ) /  mod1[i];
        unitD2[i][1] = ( unitD1[i][3] - unitD1[i][1] ) /  mod1[i];
        unitD2[i][2] = ( unitD1[i+1][2] - unitD1[i+1][0] ) /  mod1[i+1];
        unitD2[i][3] = ( unitD1[i+1][3] - unitD1[i+1][1] ) /  mod1[i+1]; // please remember this is different

    }
    let mut unitD3:Vec<f64> = Vec::new();
    for i in 0..unitD2.len(){
        unitD3.push(unitD2[i][0]);
        unitD3.push(unitD2[i][1]);

    }
    unitD3.push(unitD2[b-1][2]);
    unitD3.push(unitD2[b-1][3]);


    println!("{:?}unit vector",unitD3);

    unitD3
}

fn ind(mut arr : &mut Vec<f64>) -> Vec<i32>{
    let arrow = arrow(&mut arr);
    let mut a = arrow.len();
    let mut inda:Vec<i32> = Vec::new();

    let mut count = (a / 8) -1 ;

    for i in 0..=count{
        i as i32;
        inda.push(i as i32);

    }

    let mut inda1 = Vec::new();
    for i in 0..inda.len() {
        inda1.push(inda[i] * 4 +3);
        inda1.push(inda[i] * 4);
        inda1.push(inda[i] * 4 +2);
        inda1.push(inda[i] * 4 +2);
        inda1.push(inda[i] * 4 );
        inda1.push(inda[i]* 4 + 1);
    }

    print!("{:?}arrow inda1 Vv", inda1);


    inda1
}









    // let mut gui = Vec::new(); // this is the coordinate of the arrow.
    // for i in (0..).filter(|x| x % 8 == 0) { //each 4 points has 4 arrow points.
    //     gui.push(FIN1[i]);
    //     gui.push(FIN1[i + 1]); //because it is double distance
    //     gui.push(0.009999999776482582);
    //     gui.push(0.0);
    //     gui.push(1.0);
    //     gui.push(9.970000267028809);
    //
    //
    //     gui.push(FIN1[i + 2]);
    //     gui.push(FIN1[i + 3]);
    //     gui.push(0.009999999776482582);
    //     gui.push(0.0);
    //     gui.push(0.0);
    //     gui.push(9.970000267028809);
    //
    //     gui.push(FIN1[i + 4]);
    //     gui.push(FIN1[i + 5] * DISTANCE);
    //     gui.push(0.009999999776482582);
    //     gui.push(1.0);
    //     gui.push(0.0);
    //     gui.push(9.970000267028809);
    //
    //     gui.push(FIN1[i + 6]);
    //     gui.push(FIN1[i + 7]);
    //     gui.push(0.009999999776482582);
    //     gui.push(1.0);
    //     gui.push(1.0);
    //     gui.push(9.970000267028809);
    // }

//
//     println!("{:?} arf is:", arf); // just temporarily forget the arrow the current thought is to set the ratio
//
//     Bond
// }

    // let mut arf :Vec<f64> = Vec::new();
    // arf.push(0.2784); // RBG color index, which is fixed
    // arf.push(0.2784);
    // arf.push(0.2784);
    // for i in (0..FIN.len()-1).filter(|x| x % 2 ==0 ){
    //     arf.push(FIN[i]);
    //     arf.push(FIN[i+1]); // add the new coordinate into the matrix
    //     arf.push(0.009999999776482582);// all of the rest are fixed number
    //     arf.push(0.0);
    //     arf.push(0.0);
    //     arf.push(1.0);
    //     arf.push(0.3411764705882353);
    //     arf.push(0.5058823529411764);
    //     arf.push(1.0);
    //     arf.push(9.970000267028809);
    //
    //
    //
    // println!("{:?}arf :",arf


// fn unit(mut arr: &mut Vec<f64>) -> Vec<f64> { // remember to change it
//     let arr1 = vec![-1.0, -1.732, 0.0, 0.0, 1.0, 1.732]; // test

    //
    // let width = 2;
    // let height = arr1.len()/2;
    //
    // let mut unitD:Vec<Vec<f64>> = vec![vec![0.0; width ]; height];  // unit direction calculation
    //
    // for i in (0..arr1.len()-1).filter(|x| x % 2 ==0){ // 把原有的数据变成每两个坐标一组
    //
    //     unitD[i/2][0] = arr1[i];
    //     unitD[i/2][1] = arr1[i + 1];
    //
    //
    // }
    //
    // let width1 = 4;
    // let height1 = arr1.len()/2-1;
    // let mut unitD1:Vec<Vec<f64>> = vec![vec![0.0; width1 ]; height1]; //set every two points as a group. // 每两个向量一组
    //
    // for i in 0..unitD.len()-1 {
    //     unitD1[i][0] = unitD[i][0];
    //     unitD1[i][1] = unitD[i][1];
    //     unitD1[i][2] = unitD[i+1][0];
    //     unitD1[i][3] = unitD[i+1][1];
    //
    //
    // }
    // let mut mod1:Vec<f64> = Vec::new();// thr distance between two points.

    // for i in 0..unitD1.len(){
    //
    //     mod1.push( ((unitD1[i][2]  -unitD1[i][0] )* (unitD1[i][2]  -unitD1[i][0] ) + (unitD1[i][3]
    //         -unitD1[i][1]) *(unitD1[i][3]
    //         -unitD1[i][1])).sqrt());
    // }


    //从这里开始 稍微修改一下

    // let width2 = 4;
    // let height2 = unitD1.len()-1;
    // let mut unitD2:Vec<Vec<f64>> = vec![vec![0.0; width2 ]; height2]; //set every two points as a group.
    // // that is the calculation of the vector
    // for i in 0..unitD1.len()-1{
    //
    //     unitD2[i][0] = ( unitD1[i][0] - unitD1[i][2] ) /  mod1[i];
    //     unitD2[i][1] = ( unitD1[i][1] - unitD1[i][3] ) /  mod1[i];
    //     unitD2[i][2] = ( unitD1[i+1][2] - unitD1[i+1][0] ) /  mod1[i+1];
    //     unitD2[i][3] = ( unitD1[i+1][3] - unitD1[i+1][1] ) /  mod1[i+1]; // please remember this is different
    //
    // }
    // let mut unitD4:Vec<Vec<f64>> = vec![vec![0.0; width ]; height2]; //
    // for i in 0..unitD2.len(){
    //     if  (unitD2[i][0] + unitD2[i][2] ==0.0) && (unitD2[i][1] + unitD2[i][3] == 0.0){
    //
    //         unitD4[i][0] = unitD2[i][1] ;
    //         unitD4[i][1] = -unitD2[i][0] ;
    //
    //
    //     }else{
    //         unitD4[i][0] = unitD2[i][0] + unitD2[i][2];
    //         unitD4[i][1] = unitD2[i][1] + unitD2[i][3];
    //     }
    //
    //
    // }


    // let mut unitD3:Vec<Vec<f64>> = vec![vec![0.0; width2 ]; height2];
    // // that is the calculation of the vector
    // for i in 0..unitD1.len()-1{
    //
    //     unitD3[i][0] = ( unitD1[i][0] - unitD1[i][2] ) ;
    //     unitD3[i][1] = ( unitD1[i][1] - unitD1[i][3] );
    //     unitD3[i][2] = ( unitD1[i+1][2] - unitD1[i+1][0] );
    //     unitD3[i][3] = ( unitD1[i+1][3] - unitD1[i+1][1] ) ; // please remember this is different
    //
    // }


    // let mut theta:Vec<f64> = Vec::new(); // calculate half the angle
    //
    //
    //
    // let mut hthetal:Vec<Vec<f64>> = vec![vec![0.0; width ]; height2]; // calculate multiply of the original coordinate.
    //
    // for i in 0..unitD2.len(){
    //
    //
    //     theta.push( ((((unitD2[i][0]* unitD2[i][2])+( unitD2[i][1]* unitD2[i][3]) )).acos()) / 2.0 );
    //     //half the angle
    //     // hthetal[i][0] = (unitD4[i][0]*( DISTANCE)/ (theta[i].tan())) ;
    //     // hthetal[i][1] = (unitD4[i][1]*( DISTANCE)/ (theta[i].tan()) );
    // }


    //
    //
    // for i in 0..unitD2.len(){
    //     if theta[i] ==  1.5707963162581844{
    //         hthetal[i][0]=  (unitD4[i][0]* DISTANCE);
    //         hthetal[i][1]=  (unitD4[i][1]* DISTANCE);
    //     }else{
    //         hthetal[i][0] = (unitD4[i][0]*( DISTANCE)/ (theta[i].tan())) ;
    //         hthetal[i][1] = (unitD4[i][1]*( DISTANCE)/ (theta[i].tan()) );
    //     }
    //
    // }


//
//     let mut ang1 :Vec<Vec<f64>> = vec![vec![0.0; width ]; 2]; // x and y
//     let mut len = arr1.len() ;
//     let mut len1 = mod1.len();

    // let mut x = arr1[0] + (- unitD2[0][0]* (mod1[0]-2.5)) ;
    // let mut y = arr1[1] + (- unitD2[0][1]* (mod1[0]-2.5)) ; // we now change it into 1 // 0.5 is the ratio of the line. which is revertible.

    // let x1 = x + ang1[0][0];
    // let x1 = y + ang1[0][1];


    // ang1[0][0] = - unitD2[0][0]; //起始向量的单位向量
    // ang1[0][1] = - unitD2[0][1];


    //
    // let mut x = arr1[0] + (- unitD2[0][0]* (mod1[0]-0.5)) ;
    // let mut y = arr1[1] + (- unitD2[0][1]* (mod1[0]-0.5)) ;
    //
    // let mut m:f64 = - unitD2[0][0]; //起始向量的单位向量
    // let mut n:f64 = - unitD2[0][1];

    // ang1[1][0]=((arr1[len-2]-arr1[len-4])/mod1[len1-1]);
    // ang1[1][1]=((arr1[len-1]-arr1[len-3])/mod1[len1-1]);
//     let mut ang:Vec<Vec<f64>> = vec![vec![0.0; width]; 2] ; // direction matrix
//
//     let mut theta1:f64 = 0.2616;
//
//     ang[0][0]=theta1.cos();
//     ang[0][1]=-theta1.sin();
//     ang[1][0]=theta1.sin();
//     ang[1][1]=theta1.cos();
// // 尽量不要使用库 因为栈的内存 占用太大。
//
//     let mut end :Vec<Vec<f64>> = vec![vec![0.0; width ];1];
//     let mut E: Vec<f64> = Vec::new();
//
//
//     let mut x = arr1[0] + (- unitD2[0][0]* (mod1[0]-0.5)) ;
//     let mut y = arr1[1] + (- unitD2[0][1]* (mod1[0]-0.5)) ;
//
//     let mut m:f64 = - unitD2[0][0]; //起始向量的单位向量
//     let mut n:f64 = - unitD2[0][1];
//
//
//     let mut E1: Vec<f64> = Vec::new(); // E1 直接就是点的坐标， 我们在E1的基础上进行点的连续性 曲率更改
//     // E.push(x);
//     // E.push(y);
//
//     for i in 0..6{ // 第一次做一个迭代器，好像很简陋
//         end[0][0] =  (ang[0][0] * m + ang[0][1] * n)  ;
//         end[0][1] =  (ang[1][0] * m + ang[1][1] * n)   ;
//
//         m = end[0][0] ;
//         n = end[0][1]  ;
//
//
//         E.push(m);
//         E.push(n);
//
//     }
//
//     E1.push(x);
//     E1.push(y);
//
//     // let mut l =2.5;
//     // let mut k =2.5;
//
//     for i in (0..E.len()-1).filter(|x| x % 2 == 0){
//
//         x += 0.13 *E[i] ;
//         y += 0.13 *E[i+1] ;
//
//         E1.push(x );
//         E1.push(y);
//
//     }
//
//
//     // end[1][0] =  turn[0][0] * turn1[1][0] + turn[0][1] * turn1[1][1];
//     // end[1][1] =  turn[1][0] * turn1[1][0] + turn[1][1]  * turn1[1][1] ;
//     // let mut x1 = x + end[0][0];
//     // let mut y1 = y + end[0][1];
//     //
//
//
//
//
//
//
//
//
//     println!("{:?}end", end);
//     println!("{:?}E", E);
//     println!("{:?}E1", E1);
//     print!("{} x is" , x);
//
//
//


//     let mut turn1 :Vec<Vec<f64>> = vec![vec![0.0; width ]; 2]; // x and y
//     let mut len = arr1.len() ;
//     let mut len1 = mod1.len();
//
//     let x=  arr1[len-2] ;
//     turn1[0][0] = - unitD2[0][0];
//     turn1[0][1] = - unitD2[0][1];
//     turn1[1][0]=((arr1[len-2]-arr1[len-4])/mod1[len1-1]);
//     turn1[1][1]=((arr1[len-1]-arr1[len-3])/mod1[len1-1]);
//     let mut turn:Vec<Vec<f64>> = vec![vec![0.0; width]; 2] ; // direction matrix
//     turn[0][0]=0.0;
//     turn[0][1]=1.0;
//     turn[1][0]=-1.0;
//     turn[1][1]=0.0;
// // 尽量不要使用库 因为栈的内存 占用太大。
//     let mut turnover :Vec<Vec<f64>> = vec![vec![0.0; width ];2];
//     turnover[0][0] =  turn[0][0] * turn1[0][0] + turn[0][1] * turn1[0][1];
//     turnover[0][1] =  turn[1][0] * turn1[0][0] + turn[1][1] * turn1[0][1];
//     turnover[1][0] =  turn[0][0] * turn1[1][0] + turn[0][1] * turn1[1][1];
//     turnover[1][1] =  turn[1][0] * turn1[1][0] + turn[1][1]  * turn1[1][1] ; // 测试旋转
//
//     println!("{:?}turn over", turnover);


    //88.883056640625, 5.634969234466553, 89.00863370424325, 5.668590672464027, 89.12124291819683,
    // 5.733545771823192, 89.21322176880393, 5.825414651434269, 89.27831153906918, 5.937946077297526,
    // 89.31208318417738, 6.063482828835413, 89.31223870662362, 6.193482735807567 // E1 for left

//     let ncdRL: Vec<f64> = vec![85.63346099853516, 5.634969234466553, 88.883056640625, 5.634969234466553,
//                                89.00863370424325, 5.668590672464027, 89.12124291819683, 5.733545771823192,
//                                89.21322176880393, 5.825414651434269, 89.27831153906918, 5.937946077297526,
//                                89.31208318417738, 6.063482828835413, 89.31223870662362, 6.193482735807567, 89.383056640625, 27.468494415283203];
//
//     let ncdRa: Vec<f64> = vec![85.63346099853516, 3.634969234466553, 88.883056640625, 3.6349692344665527,
//                                89.51094195871624, 3.8030764244539204, 90.0739880284841, 4.12785192124975,
//                                90.53388228151965, 4.587196319305132, 90.85933113284587, 5.149853448621418,
//                                91.02818935838691, 5.777537206310853, 91.02896697061811, 6.4275367411716235, 91.383056640625, 27.468494415283203];
//
//     //85.63346099853516, 3.634969234466553,88.883056640625, 3.6349692344665527,
//     //                              89.02660659510987, 3.6539571914593245, 89.65767870750015, 3.8641722377366414,
//     //                              90.21190749319672, 4.236453869449434, 90.64956415584541, 4.74866193394681,
//     //                              90.85933113284587, 5.149853448621418, 91.38308935838691, 5.777537206310853,91.383056640625, 27.468494415283203
//
//
// //
// //     let mut E2:Vec<f64>  = Vec::new(); // qiantouwanxurengjiuc congmingrentaiduo youshenmeyong
// //
// //     let mut theta2:f64 = 1.57;
// //
//     let mut ang2 :Vec<Vec<f64>> = vec![vec![0.0; width ]; 2]; // x and y
//
//     ang2[0][0] = theta2.cos();
//     ang2[0][1] = theta2.sin();
//     ang2[1][0] =-theta2.sin();
//     ang2[1][1] = theta2.cos();
//
//     let mut l =  ang2[0][0] * m +  ang2[0][1] * n;
//     let mut k =  ang2[1][0] * m +  ang2[1][1] * n; // 做左边的 右边比较正好
//
//     let mut R:Vec<f64> = Vec::new();
//
//     let r1 = 1.1 ;
//     let r2 = 1.25 as f64;
//     let r3 = 1.4285 as f64 ;
//     let r4 = 1.667 as f64 ;
//     let r5 =  2.0 as f64 ;
//     let r6 =  2.2 as f64 ;
//     R.push(r1);
//     R.push(r2);
//     R.push(r3);
//     R.push(r4);
//     R.push(r5);
//     R.push(r6);
//
//
//     let mut R1:Vec<f64> = Vec::new();
//
//     let rr1 = 0.48 ;
//     let rr2 = 0.48 as f64;
//     let rr3 = 0.48 as f64 ;
//     let rr4 = 0.48 as f64 ;
//     let rr5 =  0.28 as f64 ;
//     let rr6 =  0.48 as f64 ;
//     R1.push(rr1);
//     R1.push(rr2);
//     R1.push(rr3);
//     R1.push(rr4);
//     R1.push(rr5);
//     R1.push(rr6); // for left
//
//
//
//     let a =  89.383056640625   - 0.5 ;
//     let b =  5.634969234466553 + 0.5 ;
//     //91.383056640625, 3.6349692344665527
//     //89.383056640625, 5.634969234466553   L
//
//     let theta3:f64 =0.1308;
//     for i in (0..E1.len()-2).filter(|x| x % 2 == 0){
//
//
//         E2.push(E1[i]+ (-((E1[i] -a) / 2.5 )) * R[i/2]* theta3.sin()*theta3.tan() );
//         E2.push(E1[i+1] + (-((E1[i+1] -b ) / 2.5 )) * R[i/2]* theta3.sin()*theta3.tan() );
//         // R[i] * (theta1/2).sin();
//     }
//
//     let mut E3: Vec<f64> = Vec::new();
//     for i in (0..E1.len()-2).filter(|x| x % 2 == 0){
//         E3.push(E2[i] + (theta2.cos() * (-((E1[i] -a) / 2.5 )) + (theta2.sin() * (-((E1[i+1] -b ) / 2.5 ))) )* R[i/2]* 0.1305);
//         E3.push(E2[i+1] + (-theta2.sin() * (-((E1[i] -a) / 2.5 )) + (theta2.cos() * (-((E1[i+1] -b ) / 2.5 ))) )* R[i/2] * 0.1305);
//
//     }
//
//     println!("{:?}E3", E3);
//     println!("{:?}E2", E2);
//     println!("{:?}R", R);
//
//
//     // end[0][0] =  (1.57.cos() * (-((E1[i] -a) / 0.5 )) + (-1.57.sin() * (-((E1[i+1] -b ) / 0.5 ))) )* R[i]* 0.1308 ;
//     // end[0][1] =  (1.57.sin() * (-((E1[i] -a) / 0.5 )) + 1.57.cos() * (-((E1[i+1] -b ) / 0.5 ))) * R[i] * 0.1308 ;
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//     let mut FIN1 :Vec<f64> = Vec::new(); //final
//
//     for i in (0..ncdRa.len()-3).filter(|x| x  % 2 ==0 ){
//         FIN1.push(ncdRa[i]);
//         FIN1.push(ncdRa[i + 1]);
//         FIN1.push(ncdRa[i + 2]);
//         FIN1.push(ncdRa[i + 3]);
//
//         FIN1.push(ncdRL[i +2 ]);
//         FIN1.push(ncdRL[i + 3]);
//         FIN1.push(ncdRL[i]);
//         FIN1.push(ncdRL[i + 1]);
//
//     }
//
//     let mut arf :Vec<f64> = Vec::new();
//     arf.push(0.2784); // RBG color index, which is fixed
//     arf.push(0.2784);
//     arf.push(0.2784);
//     for i in (0..FIN1.len()-1).filter(|x| x % 2 ==0 ){
//         arf.push(FIN1[i]);
//         arf.push(FIN1[i+1]); // add the new coordinate into the matrix
//         arf.push(0.009999999776482582);// all of the rest are fixed number
//         arf.push(0.0);
//         arf.push(0.0);
//         arf.push(1.0);
//         arf.push(0.3411764705882353);
//         arf.push(0.5058823529411764);
//         arf.push(1.0);
//         arf.push(9.970000267028809);
//
//
//     }
//     println!("{:?}arf :",arf);
//     print!("{} x is" , x);
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//
//     println!("{:?}",unitD);
//     println!("{:?}d1",unitD1);
//     println!("{:?}mod1",mod1);
//     println!("{:?}d2",unitD2);
//     println!("{:?}d3", unitD3);
//     println!("{:?}d4", unitD4);
//     println!("{:?}theta",theta);
//     println!("{:?}h1", hthetal);
//     // println!("{:?}turn1", turn1);
//     print!("{}x" , x) ;
//     println!("{:?}fin", FIN1);
//
//     E1
//
//
// }


//接下来我们要做的 事情 就是 更改曲率半径。 首先角度还是15度
// 1. 我们设置 五个不同的曲率。 15/10， 14/10 13/10 12/10 11/10 ，这样他的曲率就是连续的， 半径是曲率的倒数
// 2. E1得出的点应该和 下标的点链接， 得出一个方向向量 再继续向右偏转（可以把偏转向量矩阵单独写出一个方法）
// 3. 半径大小能改变的就是弦长的大小。中午想回家睡觉。
// 4. 在人间有谁活着不像是一场炼狱， 我不哭， 也许低下头会哭泣艳阳血
// 5.如果占的内存过大，我们也许可以精简一下这一步
// 6. 一生与冷漠做邻居。 折线顺时针旋转的时候，右侧的弧度就会相对较小；沿顺时针旋转的时候 右侧弧度相对就会变大，我们把它设为 偏六个 就会有七个点， 偏四次就会有五个点 垓下一曲离乱楚歌声四方。
// 7. 如果是锐角的画记得改变每一个角的大小
// 8. 内外的下标点可以做成一个 偏转四次 每一个就是11.25度，可以直接计算
// 9. 万万没想到卡在了编程上

// 10. 之后可以做锐角 Degree = 1080 - theta (jia jiao) /6 或者是4
// 11. 里面用0.5 外面用2.5 要额外添加两个点
//
// fn curvature(mut arr: &mut Vec<f64>) -> Vec<Vec<f64>>{ // realise the smoothing of the curvature.
//     let E1 = unit(&mut arr); // 调用一下上面的函数
// let mut E2:Vec<f64>  = Vec::new(); // qiantouwanxurengjiuc
//
// let mut theta1:f64 = 1.57;
//
// ang[0][0]=theta1.cos();
// ang[0][1]=-theta1.sin();
// ang[1][0]=theta1.sin();
// ang[1][1]=theta1.cos();
//
// end[0][0] =  ang[0][0] * m + ang[0][1] * n;
// end[0][1] =  ang[1][0] * m + ang[1][1] * n;
//
//
// for i in 0..E1.len(){
//     break
// }
// }

// fn handle(arr: &Vec<f64>) -> Vec<f64> { // handle the vector in order to cut off the former two. // it was used to add return type.
//         let mut hdv: Vec<f64> = Vec::new(); //
//         // let mut hdv1:Vec<f64> = Vec::new();
//         for i in 2..arr.len() {
//             hdv.push(arr[i]);
//         }
//
//
//         println!("{:?} data cut is:", hdv);
//
//
//         hdv
// }
//
// fn handle1(arr: &Vec<f64>) -> Vec<f64> { // handle the vector in order to cut off the former two. // it was used to add return type.
//
//         let mut hdv1: Vec<f64> = Vec::new();
//
//         for i in 0..2 {
//             hdv1.push(arr[i]);
//         }
//         println!("{:?} data cut is:", hdv1);
//
//
//         hdv1
// }
//
// fn rectangular(){
//   let mut A = N * 180.0; //square of multiple line pattern is
//   let mut angle :Vec<f64> = Vec::new();
//   let mut angle1 :Vec<f64> = Vec::new();
//
//   for i in 0..angle.len(){
//     angle1.push (180.0- ( A - 180.0 - angle[i] ) / (N - 1) );
//   }
//
//
// }

// if clo[0] > 0.0 {
//     Or.push(er[0]);
//     Or.push(er[1]);
// }else{
//     Or.push(el[0]);
//     Or.push(el[1]);

// }
// for i in (0..arr1.len() - 4).filter(|x| x % 2 == 0){
//     Or.push(ncdR[2 * i]);
//     Or.push(ncdR[2 * i + 1]);
//     Or.push(arr1[i+2]+cal[i/2][0]);
//     Or.push(arr1[i+3]+cal[i/2][1]);
//     Or.push(ncdR[2 * (i + 2)]);
//     Or.push(ncdR[2 * (i + 2) + 1]);
// }
// if clo[0] > 0.0 {
//     Or.push(er[2]);
//     Or.push(er[3]);
// }else{
//     Or.push(el[2]);
//     Or.push(el[3]);
//
//
// } // }else if theta[i] < 1.5707963162581844{  // temp = acutAR(&mut sort[i]);
//         //     if clo[i] > 0.0{
//         //         let (temp1,temp) = acutAR(&mut sort[i]);
//         //
//         //     }else {
//         //         let (temp,temp1) = acutAR(&mut sort[i]); // temp.extend_from_slice(&t);
//         //
//         //     } // temp.push(sort[i][4]); // temp.push(sort[i][5]) //
//         //     for i in 0..temp.len() {
//         //         roundL.push(temp1[i]);
//         //     }
