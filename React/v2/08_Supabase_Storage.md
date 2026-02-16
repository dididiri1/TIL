# Supabase Storage

## 버킷 생성
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/08_01.png?raw=true)

![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/08_02.png?raw=true)

## 버킷 정책 생성
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/08_03.png?raw=true)


```
-- 조회: 모든 사용자 (SNS 특성상 공개)
CREATE POLICY "Anyone can view uploads" ON storage.objects
FOR SELECT USING (bucket_id = 'uploads');

-- 업로드: 자신의 폴더에만 업로드 가능
CREATE POLICY "Users can upload to own folder" ON storage.objects
FOR INSERT WITH CHECK (
  bucket_id = 'uploads' AND 
  auth.uid()::text = (storage.foldername(name))[1]
);

-- 수정: 자신의 폴더 아래의 파일만 수정 가능
CREATE POLICY "Users can update own files" ON storage.objects
FOR UPDATE USING (
  bucket_id = 'uploads' AND 
  auth.uid()::text = (storage.foldername(name))[1]
);

-- 삭제: 자신의 폴더 아래의 파일만 삭제 가능
CREATE POLICY "Users can delete own files" ON storage.objects
FOR DELETE USING (
  bucket_id = 'uploads' AND 
  auth.uid()::text = (storage.foldername(name))[1]
);
```
### 정책 확인
![](https://github.com/dididiri1/TIL/blob/main/React/v2/images/08_04.png?raw=true)

