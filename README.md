# Lidar
import math

# parameters captured from LiDAR, using first scan from the captured file due to lack of access to the real LiDAR. 
# the minimum angle in radians of the LiDAR scan range
angle_min = 0.0
# the maximum angle in radians of the LiDAR scan range
angle_max = 6.2831854820251465
# The angular step of the beam in radians.
angle_increment = 0.013809198513627052

# ranges from LiDAR
# because of the presence of "nan" in the captured values, in order to process the data I used .split to split the string in comma delimited format. 
ranges_str = "[1.312999963760376, 1.281999945640564, 1.2350000143051147, 1.2200000286102295, 1.2039999961853027, 1.1890000104904175, 1.1579999923706055, 1.1269999742507935, 1.1109999418258667, 1.0959999561309814, 1.0800000429153442, 1.0490000247955322, 1.0490000247955322, 1.0180000066757202, 1.0019999742507935, 0.9869999885559082, 0.6150000095367432, 0.5989999771118164, 0.5989999771118164, 0.5839999914169312, 0.5839999914169312, 0.5839999914169312, 0.5529999732971191, 0.5529999732971191, 0.5220000147819519, 0.5059999823570251, 0.4860000014305115, 0.48100000619888306, 0.47099998593330383, 0.46299999952316284, 0.45899999141693115, 0.45100000500679016, 0.4490000009536743, 0.44600000977516174, 0.4390000104904175, 0.4390000104904175, 0.4390000104904175, 0.4390000104904175, 0.37599998712539673, 0.38499999046325684, 0.38499999046325684, nan, nan, 0.3009999990463257, 0.3009999990463257, 0.3009999990463257, 0.30000001192092896, 0.29899999499320984, 0.296999990940094, 0.2930000126361847, 0.296999990940094, 0.2919999957084656, 0.29499998688697815, 0.2879999876022339, 0.28700000047683716, 0.2849999964237213, 0.2809999883174896, 0.27399998903274536, 0.27399998903274536, 0.27399998903274536, 0.27399998903274536, 0.27399998903274536, 0.27399998903274536, 0.27399998903274536, 0.27399998903274536, 0.27399998903274536, 0.27300000190734863, 0.2709999978542328, 0.2680000066757202, 0.27399998903274536, 0.27300000190734863, 0.2709999978542328, 0.2680000066757202, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2619999945163727, 0.2630000114440918, 0.26499998569488525, 0.2680000066757202, 0.2619999945163727, 0.2619999945163727, 0.2630000114440918, 0.26499998569488525, 0.2680000066757202, 0.2750000059604645, 0.2750000059604645, 0.2750000059604645, 0.2750000059604645, 0.2750000059604645, 0.2759999930858612, 0.2770000100135803, 0.27900001406669617, 0.28299999237060547, 0.2770000100135803, 0.27900001406669617, 0.28299999237060547, 0.2919999957084656, 0.29600000381469727, 0.28999999165534973, 0.29100000858306885, 0.2930000126361847, 0.296999990940094, 0.2930000126361847, 0.2980000078678131, 0.29499998688697815, 0.3019999861717224, 0.3019999861717224, 0.3019999861717224, 0.3019999861717224, 0.30300000309944153, 0.3050000071525574, 0.30799999833106995, 0.3149999976158142, 0.3160000145435333, 0.3179999887943268, 0.32199999690055847, 0.32899999618530273, 0.33000001311302185, 0.3319999873638153, 0.33500000834465027, 0.34200000762939453, 0.34200000762939453, 0.34299999475479126, 0.3569999933242798, 0.36000001430511475, 0.3659999966621399, 0.36399999260902405, 0.37299999594688416, 0.3779999911785126, 0.3880000114440918, 0.3919999897480011, 0.4000000059604645, 0.40299999713897705, 0.4090000092983246, 0.41999998688697815, 0.42800000309944153, 0.4300000071525574, 0.4350000023841858, 0.4320000112056732, 0.4259999990463257, 0.6150000095367432, 0.6150000095367432, 0.6299999952316284, 0.6460000276565552, 0.6610000133514404, 0.6769999861717224, 0.6919999718666077, 0.6919999718666077, 0.7080000042915344, 0.7229999899864197, 0.7229999899864197, 0.7540000081062317, 0.7699999809265137, 0.7850000262260437, 0.8009999990463257, nan, nan, nan, nan, 0.9089999794960022, 0.925000011920929, 0.9399999976158142, nan, nan, 1.2350000143051147, 1.2350000143051147, 1.2200000286102295, 1.2350000143051147, nan, 6.089000225067139, nan, nan, nan, nan, nan, nan, nan, nan, nan, 5.9029998779296875, 5.9029998779296875, 5.9029998779296875, 5.9029998779296875, 5.406000137329102, 5.436999797821045, 4.072999954223633, 3.9800000190734863, 3.9019999504089355, 3.8559999465942383, 3.7939999103546143, nan, 3.7780001163482666, 2.1500000953674316, 2.11899995803833, 2.11899995803833, 1.3589999675750732, 1.3279999494552612, 1.3279999494552612, 1.312999963760376, 1.312999963760376, 1.2970000505447388, 1.312999963760376, 1.2970000505447388, 1.312999963760376, 1.312999963760376, 1.312999963760376, 0.3449999988079071, 0.33500000834465027, 0.328000009059906, 0.3269999921321869, 0.32499998807907104, 0.32100000977516174, 0.3140000104904175, 0.3140000104904175, 0.31299999356269836, 0.3109999895095825, 0.30799999833106995, 0.3149999976158142, 0.3149999976158142, 0.3149999976158142, 0.3160000145435333, 0.3179999887943268, 0.32199999690055847, 0.32899999618530273, 0.32899999618530273, 0.32899999618530273, 0.33000001311302185, 0.3310000002384186, 0.33399999141693115, 0.33899998664855957, 0.335999995470047, 0.3440000116825104, 0.34599998593330383, 0.3499999940395355, 0.35899999737739563, 0.36399999260902405, 0.37299999594688416, 0.3790000081062317, 0.39100000262260437, 0.39800000190734863, 2.134000062942505, 0.37599998712539673, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.367000013589859, 0.3659999966621399, 0.36500000953674316, 0.3619999885559082, 0.3569999933242798, 0.34599998593330383, 0.33799999952316284, 0.33399999141693115, 0.3269999921321869, 0.3269999921321869, nan, 0.3269999921321869, 0.3269999921321869, 0.32600000500679016, 0.3240000009536743, 0.3199999928474426, 0.31200000643730164, 0.3089999854564667, 0.3160000145435333, 0.31700000166893005, 0.3190000057220459, 0.3240000009536743, 0.32100000977516174, 0.3149999976158142, 0.3149999976158142, 0.3149999976158142, 0.3149999976158142, 0.3149999976158142, 0.3149999976158142, 0.3160000145435333, 0.3179999887943268, 0.32100000977516174, 0.328000009059906, 0.328000009059906, 0.328000009059906, 0.328000009059906, 0.328000009059906, 0.328000009059906, 0.328000009059906, 0.328000009059906, 0.328000009059906, 0.32899999618530273, 0.33000001311302185, 0.3330000042915344, 0.33899998664855957, 0.3499999940395355, 0.3720000088214874, 0.3889999985694885, 0.3930000066757202, 0.3880000114440918, 0.39100000262260437, 0.3840000033378601, 0.3840000033378601, 0.382999986410141, 0.38100001215934753, 0.37700000405311584, 0.38600000739097595, 0.3869999945163727, 0.38999998569488525, 0.39500001072883606, 0.3930000066757202, 0.3880000114440918, 0.39100000262260437, 0.39800000190734863, 0.34299999475479126, 0.3310000002384186, 0.32100000977516174, 0.3149999976158142, 0.3019999861717224, 0.30300000309944153, 0.2919999957084656, 0.28200000524520874, 0.2770000100135803, 0.26600000262260437, 0.25699999928474426, 0.23999999463558197, 0.23100000619888306, 0.22699999809265137, 0.2199999988079071, 0.21899999678134918, 0.21699999272823334, 0.21299999952316284, 0.20399999618530273, 0.19900000095367432, 0.20200000703334808, 0.20800000429153442, 0.20800000429153442, 0.20800000429153442, 0.20800000429153442, 0.20800000429153442, 0.2070000022649765, 0.21799999475479126, 0.2150000035762787, 0.22100000083446503, 0.22100000083446503, 0.2199999988079071, 0.21899999678134918, 0.2160000056028366, 0.210999995470047, 0.21400000154972076, 0.20600000023841858, 0.2029999941587448, 0.19699999690055847, 0.19900000095367432, 0.1899999976158142, 0.18400000035762787, 0.17299999296665192, 0.16500000655651093, nan, 0.16099999845027924, 0.17599999904632568, 0.18400000035762787, 0.18700000643730164, 0.19300000369548798, 0.1899999976158142, 0.1850000023841858, 0.1889999955892563, 0.19599999487400055, 0.19699999690055847, 0.21199999749660492, 0.2150000035762787, 0.22200000286102295, 0.22200000286102295, 0.5519999861717224, 0.5370000004768372, 0.5370000004768372, 0.5210000276565552, 0.5210000276565552, 0.5210000276565552, 0.5210000276565552, 0.5210000276565552, 0.5210000276565552, 0.5210000276565552, 0.5210000276565552, 0.5210000276565552, 0.5059999823570251, 0.5059999823570251, 0.5210000276565552, 0.5370000004768372, 0.7699999809265137, 0.7229999899864197, 0.6919999718666077, 0.6919999718666077, 0.7080000042915344, 0.7080000042915344, 0.7229999899864197, 3.2820000648498535, 3.2660000324249268, 3.234999895095825, nan, nan, nan, 3.5299999713897705, 3.4519999027252197, 3.499000072479248, 3.499000072479248, 3.499000072479248, 3.499000072479248, nan, nan, 3.4830000400543213, 3.4679999351501465, 3.4679999351501465, 3.4679999351501465, 3.4679999351501465, 3.4679999351501465, 3.4679999351501465, 3.4679999351501465, 3.4679999351501465, 3.4679999351501465, 3.4830000400543213, nan, 1.3279999494552612, 1.3279999494552612, 1.3279999494552612, 1.715000033378601, 1.715000033378601, 1.715000033378601, 1.6690000295639038, nan, nan, nan, nan, nan, 1.715000033378601, 1.684000015258789, 1.6690000295639038, nan, nan, nan, nan, nan, nan, nan, nan]"
ranges = [float(x) if x != "nan" else math.nan for x in ranges_str[1:-1].split(", ")]

# calculating the angle of the closest object
min_distance = min(ranges)
min_index = ranges.index(min_distance)
angle = angle_min + min_index * angle_increment
angle_degrees = math.degrees(angle)
angle_360 = angle_degrees if angle_degrees >= 0 else angle_degrees + 360

print("Minimum Distance: ", min_distance)
print("Angle in Radians: ", angle)
print("Angle in Degrees: ", angle_360)
